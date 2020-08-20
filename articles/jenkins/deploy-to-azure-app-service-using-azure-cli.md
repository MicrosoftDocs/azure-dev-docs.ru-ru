---
title: Руководство. Развертывание в Службе приложений Azure с помощью Jenkins и Azure CLI
description: Сведения об использовании Azure CLI для развертывания веб-приложения Java в Azure в конвейере Jenkins
keywords: jenkins, azure, devops, app service, cli
ms.topic: tutorial
ms.date: 08/08/2020
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 9c5f3b30507cda764dd29f0c133d4997dbc2bb53
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240956"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Руководство по Развертывание в службу приложений Azure с помощью Jenkins и Azure CLI

Для развертывания веб-приложения Java в Azure можно использовать Azure CLI в [конвейере Jenkins](https://jenkins.io/doc/book/pipeline/). В этом учебнике описано, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Настройка Jenkins
> * Создание веб-приложения в Azure
> * Подготовка репозитория GitHub
> * Создание конвейера Jenkins
> * Запуск конвейера и проверка веб-приложения

## <a name="create-and-configure-jenkins-instance"></a>Создание и настройка экземпляра Jenkins

Если у вас еще нет главного экземпляра Jenkins, [установите Jenkins в виртуальной машине Linux](configure-on-linux-vm.md).

Подключаемый модуль учетных данных Azure позволяет хранить учетные данные субъекта-службы Microsoft Azure в Jenkins. В версии 1.2 добавлена соответствующая поддержка, поэтому конвейер Jenkins может получать учетные данные Azure. 

Убедитесь, что у вас установлена версия 1.2 или более поздняя:

* На панели мониторинга Jenkins последовательно выберите **Manage Jenkins -> Plugin Manager ->** (Управление Jenkins -> Диспетчер подключаемых модулей) и выполните поиск **учетных данных Azure**. 
* Если используется версия более ранняя, чем 1.2, обновите подключаемый модуль.

В главном экземпляре Jenkins также требуются Java JDK и Maven. Чтобы выполнить установку, войдите в главный экземпляр Jenkins с помощью SSH-подключения и выполните следующие команды:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Добавление субъекта-службы Azure в учетные данные Jenkins

Для выполнения Azure CLI необходимы учетные данные Azure.

* На панели мониторинга Jenkins выберите **Credentials -> System ->**(Учетные данные -> Система). Щелкните **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).
* Щелкните **Add Credentials** (Добавить учетные данные), чтобы добавить [субъект-службу Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) путем ввода следующих значений: идентификатор подписки, идентификатор клиента, секрет клиента и конечная точка маркера OAuth 2.0. Укажите идентификатор, который будет использоваться в следующем шаге.

![Добавить учетные данные](./media/deploy-to-azure-app-service-using-azure-cli/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Создание службы приложений Azure для развертывания веб-приложения Java

Создайте план службы приложений Azure с ценовой категорией **Бесплатный** с помощью команды CLI [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). От плана службы приложений зависят физические ресурсы, используемые для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, которые позволяют сэкономить при размещении нескольких приложений. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

После создания плана в Azure CLI отобразится приблизительно такой результат:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure

 С помощью команды CLI [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) создайте определение веб-приложения в плане службы приложений `myAppServicePlan`. Определение веб-приложения предоставляет URL-адрес для доступа к приложению и настраивает несколько параметров для развертывания кода в Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Замените заполнитель `<app_name>` уникальным именем своего приложения. Это уникальное имя используется в доменном имени по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить запись личного доменного имени с веб-приложением, прежде чем предоставлять его пользователям.

Когда вы создадите определение веб-приложения, в Azure CLI отобразятся следующие сведения: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Настройка Java

Настройте конфигурацию среды выполнения Java, необходимую для работы приложения, с помощью команды [az appservice web config update](/cli/azure/webapp/config).

Следующая команда настраивает веб-приложение для запуска в последней версии JDK Java 8 и [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Подготовка репозитория GitHub

1. Откройте репозиторий [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Простое веб-приложение Java для Azure). Чтобы создать разветвление репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Разветвление) в правом верхнем углу.

1. В пользовательском веб-интерфейсе GitHub откройте файл **Jenkinsfile**. Щелкните значок с изображением карандаша, чтобы изменить этот файл для обновления группы ресурсов и имени веб-приложения в строках 20 и 21, соответственно.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<app_name>'
    ```
    
1. Измените строку 23 для обновления идентификатора учетных данных в вашем экземпляре Jenkins

    ```java
    withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Создание конвейера Jenkins

Откройте Jenkins в веб-браузере, щелкните **New Item** (Создать элемент).

1. Введите имя для задания.
1. Выберите **Конвейер**. 
1. Щелкните **ОК**.
1. Выберите **Конвейер**.
1. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).
1. Для параметра **SCM** выберите значение **Git**.
1. Введите URL-адрес GitHub для вилки репозитория: `https:\<your forked repo\>.git`.
1. Нажмите кнопку **Сохранить**.

## <a name="test-your-pipeline"></a>Тестирование конвейера

1. Перейдите к созданному конвейеру.
1. Щелкните **Build now** (Скомпилировать).
1. После компиляции щелкните **Console Output** (Выходные данные консоли), чтобы просмотреть сведения о компиляции.

## <a name="verify-your-web-app"></a>Проверка веб-приложения

Чтобы проверить, успешно ли развернут WAR-файл в веб-приложении, сделайте следующее: 

1. Откройте веб-браузер:

1. Перейдите по адресу `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`.

1. Должен отобразиться текст, аналогичный приведенному ниже.

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Перейдите по адресу: http://&lt;имя_приложени>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (замените &lt;x> и &lt;y> любыми числами) для получения суммы x и y.

    ![Калькулятор: сложение](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Развертывание в веб-приложении Azure на платформе Linux

Если вы используете Azure CLI в конвейере Jenkins, измените скрипт для развертывания в решении "Веб-приложения Azure в Linux". Это решение поддерживает Docker. Таким образом, необходимо предоставить Dockerfile для упаковки веб-приложения со средой выполнения службы в образ Docker. Затем подключаемый модуль создаст образ, отправит его в реестр Docker и развернет в веб-приложении.

1. [Создайте веб-приложение Azure на платформе Linux](/azure/app-service/containers/quickstart-nodejs).

1. [Установите Docker в Jenkins](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. [Создайте экземпляр службы "Реестр контейнеров" на портале Azure](/azure/container-registry/container-registry-get-started-azure-cli).

1. В той же вилке репозитория [простого веб-приложения Java для Azure](https://github.com/azure-devops/javawebappsample) измените файл **Jenkinsfile2** следующим образом:

    1. Вместо заполнителей укажите имена группы ресурсов, веб-приложения и реестра ACR.

        ```bash
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    1. Замените `<azsrvprincipal\>` идентификатором учетных данных.

        ```bash
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

1. Создайте конвейер Jenkins, как вы делали это при развертывании в веб-приложении Azure в Windows с помощью `Jenkinsfile2`.

1. Запустите новое задание.

1. Для проверки выполните следующую команду в Azure CLI:

    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```

    Должен отобразиться примерно такой результат:
    
    ```output
    [
    "calculator"
    ]
    ```
    
1. Перейдите по ссылке `http://<app_name>.azurewebsites.net/api/calculator/ping` (замените заполнитель). Должен отобразиться примерно такой результат: 

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Перейдите по ссылке `http://<app_name>.azurewebsites.net/api/calculator/add?x=<x>&y=<y>` (замените заполнители). Отобразится сумма значений, заданных для `x` и `y`.
    
## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins)
