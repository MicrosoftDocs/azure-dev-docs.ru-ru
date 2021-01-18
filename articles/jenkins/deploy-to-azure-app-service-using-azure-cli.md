---
title: Руководство. Развертывание в Службе приложений Azure с помощью Jenkins и Azure CLI
description: Сведения об использовании Azure CLI для развертывания веб-приложения Java в Azure в конвейере Jenkins
keywords: jenkins, azure, devops, app service, cli
ms.topic: tutorial
ms.date: 01/06/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: cac490a111120e7b390e26d020c5a6ad00fdd667
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109067"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Руководство по Развертывание в службу приложений Azure с помощью Jenkins и Azure CLI

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Для развертывания веб-приложения Java в Azure можно использовать Azure CLI в [конвейере Jenkins](https://jenkins.io/doc/book/pipeline/). В этом учебнике описано, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Настройка Jenkins
> * Создание веб-приложения в Azure
> * Подготовка репозитория GitHub
> * Создание конвейера Jenkins
> * Запуск конвейера и проверка веб-приложения

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Jenkins** - [установите Jenkins на виртуальной машине Linux.](configure-on-linux-vm.md)
- **Azure CLI.** Установите Azure CLI (версии 2.0.67 или более поздней версии) на сервере Jenkins.

## <a name="configure-jenkins"></a>Настройка Jenkins

Ниже показано, как установить необходимые компоненты JDK Java и Maven на главном сервере Jenkins:

1. Войдите на главный сервер Jenkins с помощью SSH.

1. [Скачайте и установите JDK Azul Zulu из репозитория, используя apt-get:](/azure/developer/java/fundamentals/java-jdk-install#download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository)

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    sudo apt-get -q update
    sudo apt-get -y install zulu-8-azure-jdk
    ```
    
1. Выполните следующую команду, чтобы установить Maven:

    ```bash
    sudo apt-get install -y maven
    ```
    
## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Добавление субъекта-службы Azure в учетные данные Jenkins

Ниже показано, как указать учетные данные Azure:

1. Убедитесь, что [подключаемый модуль учетных данных](https://plugins.jenkins.io/credentials/) установлен.

1. На панели мониторинга Jenkins выберите элементы **Credentials -> System** (Учетные данные -> Система). 

1. Выберите элемент **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).

1. Чтобы добавить **субъект-службу Microsoft Azure**, выберите элемент [Add Credentials](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json) (Добавить учетные данные). Убедитесь, что выбран тип учетных данных **_Username with password_* _ (Имя пользователя с паролем), и введите следующие данные:

    _ **Имя пользователя**: субъект-служба `appId`.
    * **Пароль**. субъект-служба `password`.
    * **Идентификатор**. идентификатор учетных данных (например, `AzureServicePrincipal`).

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Создание службы приложений Azure для развертывания веб-приложения Java

Создайте план службы приложений Azure с ценовой категорией [Бесплатный](/cli/azure/appservice/plan#az-appservice-plan-create) с помощью команды **az appservice plan create**:

```azurecli
az appservice plan create \
    --name <app_service_plan> \ 
    --resource-group <resource_group> \
    --sku FREE
```

**Примечания**

- От плана службы приложений зависят физические ресурсы, используемые для размещения приложений.
- Все приложения, назначенные плану службы приложений, совместно используют эти ресурсы.
- Планы службы приложений позволяют экономить затраты при размещении нескольких приложений.

## <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure

Создайте определение веб-приложения в плане службы приложений `myAppServicePlan` с помощью команды [az webapp create](/cli/azure/webapp#az-webapp-create).

```azurecli
az webapp create \
    --name <app_name> \ 
    --resource-group <resource_group> \
    --plan <app_service_plan>
```

**Примечания**

- Определение веб-приложения предоставляет URL-адрес для доступа к приложению и настраивает несколько параметров для развертывания кода в Azure.
- Замените заполнитель `<app_name>` уникальным именем приложения.
- Имя приложения является частью доменного имени по умолчанию для веб-приложения. Следовательно, оно должно быть глобально уникальным среди всех приложений Azure.
- Позже можно сопоставить запись личного доменного имени с веб-приложением, прежде чем предоставлять его пользователям.


## <a name="configure-java"></a>Настройка Java

Настройте конфигурацию среды выполнения Java для приложения с помощью команды [az appservice web config update](/cli/azure/webapp/config):

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group <resource_group> \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Подготовка репозитория GitHub

1. Откройте репозиторий [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Простое веб-приложение Java для Azure).

1. Чтобы создать вилку репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Вилка).

1. Откройте файл **Jenkinsfile**, щелкнув имя файла.

1. Чтобы изменить файл, щелкните значок карандаша.

1. Обновите идентификатор подписки и идентификатор клиента.
    
    ```groovy
      withEnv(['AZURE_SUBSCRIPTION_ID=<subscription_id>',
            'AZURE_TENANT_ID=<tenant_id>']) 
    ```
    
1. Обновите группу ресурсов и имя веб-приложения в строках 22 и 23 соответственно.

    ```groovy
    def resourceGroup = '<resource_group>'
    def webAppName = '<app_name>'
    ```

1. Обновление идентификатора учетных данных в экземпляре Jenkins

    ```groovy
    withCredentials([usernamePassword(credentialsId: '<service_princial>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Создание конвейера Jenkins

Чтобы создать конвейер Jenkins:

1. Откройте Jenkins в веб-браузере.

1. Выберите **New Item** (Создать элемент).

1. Введите имя для задания.

1. Выберите **Конвейер**.

1. Щелкните **ОК**.

1. Выберите **Конвейер**.

1. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).

1. Для параметра **SCM** выберите значение **Git**.

1. Введите URL-адрес GitHub для вилки репозитория: `https:\<forked_repo\>.git`.

1. Нажмите кнопку **Сохранить**.

## <a name="test-your-pipeline"></a>Тестирование конвейера

1. Перейдите к созданному конвейеру.

1. Выберите элемент **Build now** (Выполнить сборку).

1. После компиляции щелкните **Console Output** (Выходные данные консоли), чтобы просмотреть сведения о компиляции.

## <a name="verify-your-web-app"></a>Проверка веб-приложения

Чтобы проверить, успешно ли развернут WAR-файл в веб-приложении:

1. Перейдите по следующему URL-адресу: `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`.

1. Должен отобразиться текст, аналогичный приведенному ниже.

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Перейдите по следующему URL-адресу (замените &lt;x> и &lt;y> двумя суммируемыми значениями): http://&lt;имя_приложения>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>.

    ![Пример добавления](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-app-service-on-linux"></a>Развертывание в Службу приложений Azure в Linux

Служба приложений также позволяет клиентам размещать веб-приложения из поддерживаемых стеков приложений изначально в Linux. Кроме того, она может запускать пользовательские контейнеры Linux (также известные как Веб-приложения для контейнеров).

Вы можете изменить скрипт для развертывания в Службе приложений Azure в Linux. Служба приложений в Linux поддерживает Docker. Таким образом, необходимо предоставить Dockerfile для упаковки веб-приложения со средой выполнения службы в образ Docker. Затем подключаемый модуль создаст образ, отправит его в реестр Docker и развернет в веб-приложении.

1. См. статью [Перенос пользовательского программного обеспечения в Службу приложений Azure с помощью пользовательского контейнера](/azure/app-service/tutorial-custom-container?pivots=container-linux#configure-app-service-to-deploy-the-image-from-the-registry), чтобы создать Службу приложений Azure в Linux и Реестр контейнеров Azure.

    ```azurecli
        az group create --name myResourceGroup2 --location westus2
        az acr create --name myACRName --resource-group myResourceGroup2 --sku Basic --admin-enabled true
        az appservice plan create --name myAppServicePlan --resource-group  myResourceGroup2 --is-linux
        az webapp create --resource-group myResourceGroup2 --plan myAppServicePlan --name myApp --deployment-container-image-name myACRName.azurecr.io/calculator:latest
    ```

1. [Установите Docker в Jenkins](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. Убедитесь, что [подключаемый модуль конвейера Docker](https://plugins.jenkins.io/docker-workflow/) установлен.

1. В той же вилке репозитория [простого веб-приложения Java для Azure](https://github.com/azure-devops/javawebappsample) измените файл **Jenkinsfile2** следующим образом:

    1. Обновите идентификатор подписки и идентификатор клиента.

        ```groovy
         withEnv(['AZURE_SUBSCRIPTION_ID=<mySubscriptionId>',
                'AZURE_TENANT_ID=<myTenantId>']) {
        ```

    1. Вместо заполнителей укажите имена группы ресурсов, веб-приложения и реестра ACR.

        ```bash
        def webAppResourceGroup = '<resource_group>'
        def webAppName = '<app_name>'
        def acrName = '<registry>'
        ```

    1. Замените `<azsrvprincipal\>` идентификатором учетных данных.

        ```bash
        withCredentials([usernamePassword(credentialsId: '<service_principal>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
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