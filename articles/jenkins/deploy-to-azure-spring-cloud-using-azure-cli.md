---
title: Развертывание приложений в Azure Spring Cloud с помощью Jenkins и Azure CLI
description: Узнайте, как развертывать микрослужбы в службе Azure Spring Cloud с помощью Azure CLI в конвейере непрерывной интеграции и развертывания
keywords: jenkins, azure, devops, azure spring cloud, azure cli
ms.topic: tutorial
ms.date: 11/10/2020
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: ef078589506af95fc05bfd7a85f48757f3cb0242
ms.sourcegitcommit: 0eb25e1fdafcd64118843748dc061f60e7e48332
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625993"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Руководство по Развертывание приложений в Azure Spring Cloud с помощью Jenkins и Azure CLI

[Azure Spring Cloud](/azure/spring-cloud/spring-cloud-overview) — это полностью управляемая среда для разработки микрослужб со встроенными функциями обнаружения служб и управления конфигурацией. Эта служба позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot. В этом руководстве показано, как использовать Azure CLI в Jenkins для автоматизации непрерывной интеграции и доставки (CI/CD) в Azure Spring Cloud.

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * подготовка экземпляра службы и запуск приложения Java Spring;
> * подготовка сервера Jenkins;
> * создание и развертывание приложений микрослужб с использованием Azure CLI в конвейере Jenkins. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Jenkins**: [установите Jenkins на виртуальной машине Linux.](configure-on-linux-vm.md)
- **Учетная запись GitHub**. Если у вас нет учетной записи GitHub, [создайте бесплатную учетную запись](https://github.com/), прежде чем начинать работу.

## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Подготовка экземпляра службы и запуск приложения Java Spring

В качестве примера приложения для службы Майкрософт мы используем [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics), для которого выполняем те же инструкции, что и в рамках [краткого руководства по запуску приложения Java Spring с помощью Azure CLI](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli), для инициализации экземпляра службы и настройки приложений. Если вы уже сделали это, можете пропустить следующий раздел. Если нет, выполните команды Azure CLI, указанные далее. В [кратком руководстве по запуску приложения Java Spring с помощью Azure CLI](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) вы найдете дополнительные сведения на эту тему.

Локальный компьютер должен соответствовать тем же предварительным требованиям, что и сервер сборки Jenkins. Чтобы выполнять сборку и развертывание приложений микрослужб, убедитесь, что установлены следующие компоненты:

* [Git](https://git-scm.com/);
* [JDK 8](/java/azure/jdk);
* [Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi);
* [установленное средство Azure CLI](/cli/azure/install-azure-cli) версии 2.0.67 или выше.

1. Установите расширение Azure Spring Cloud.

    ```azurecli
    az extension add --name spring-cloud
    ```

1. Создайте группу ресурсов, которая будет содержать службу Azure Spring Cloud.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

1. Подготовьте к работе экземпляр Azure Spring Cloud.

    ```azurecli
    az spring-cloud create -n <service name> -g <resource group name>
    ```

1. Создайте вилку репозитория [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) в своей учетной записи GitHub. На локальном компьютере клонируйте репозиторий в каталог с именем `source-code`.

    ```bash
    mkdir source-code
    git clone https://github.com/<your GitHub id>/piggymetrics
    ```

1. Настройте сервер конфигурации. Не забудьте заменить заполнитель &lt;your GitHub id&gt; реальным значением идентификатора GitHub.

    ```azurecli
    az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

1. Скомпилируйте проект.

    ```bash
    cd piggymetrics
    mvn clean package -D skipTests
    ```

1. Создайте три микрослужбы: **gateway**, **auth-service** и **account-service**.

    ```azurecli
    az spring-cloud app create --n gateway -s <service name> -g <resource group name>
    az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
    az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

1. Разверните приложение.

    ```azurecli
    az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

1. Назначьте шлюзу общедоступную конечную точку.

    ```azurecli
    az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

1. Отправьте приложению шлюза запрос на получение URL-адреса и убедитесь, что приложение успешно работает.

    ```azurecli
    az spring-cloud app show --name gateway | grep url
    ```
    
 1. Перейдите по URL-адресу, полученному с помощью предыдущей команды, чтобы запустить приложение PiggyMetrics.

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

В рамках этого раздела вы подготовите к сборке сервер Jenkins, что вполне пригодно для тестирования. Но для сборки реальных приложений по соображениям безопасности следует развернуть в Azure [агент виртуальной машины Azure](https://plugins.jenkins.io/azure-vm-agents) или [агент контейнеров Azure](https://plugins.jenkins.io/azure-container-agents). Дополнительные сведения см. в статье о [вопросах безопасности при сборке в главном узле](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) в документации по Jenkins.

### <a name="install-plug-ins"></a>Установка подключаемых модулей

1. Выполните вход на сервер Jenkins. Выберите действие **Manage Jenkins (Управление Jenkins) > Manage Plugins (Управление подключаемыми модулями)** .

1. На вкладке **Available** (Доступные) выберите такие подключаемые модули:

    * [интеграция с GitHub](https://plugins.jenkins.io/github-pullrequest);
    * [учетные данные Azure](https://plugins.jenkins.io/azure-credentials).

    Если этих подключаемых модулей нет в списке, перейдите на вкладку **Installed** (Установленные) и проверьте, установлены ли они.

1. Чтобы установить подключаемые модули, выберите **Download now and install after restart** (Скачать сейчас и установить после перезагрузки).

1. Чтобы завершить установку, перезагрузите сервер Jenkins.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Добавление учетных данных субъекта-службы Azure в хранилище учетных данных Jenkins

1. Для развертывания в Azure вам нужен субъект-служба Azure. Дополнительные сведения см. в разделе [Создание субъекта-службы](./deploy-to-azure-app-service-using-azure-cli.md#add-azure-service-principal-to-a-jenkins-credential) руководства по развертыванию в Службе приложений Azure. Результат `az ad sp create-for-rbac` должен выглядеть следующим образом:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

1. На панели мониторинга Jenkins выберите **Credentials** (Учетные данные) > **System** (Система). Затем выберите **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).

1. Щелкните **Добавление учетных данных**.

1. Выберите тип учетных данных **Субъект-служба Microsoft Azure**.

1. Введите значения в следующие поля:

    - **Идентификатор подписки**: Идентификатор подписки Azure.
    - **Идентификатор клиента**: идентификатор приложения субъекта-службы.
    - **Секрет клиента**: пароль субъекта-службы.
    - **Идентификатор арендатора**: идентификатор арендатора учетной записи Майкрософт.
    - **Среда Azure**: выберите соответствующее значение для своей среды. Например, выберите **Azure** для Azure Global.
    - **Идентификатор**. задайте значение `azure_service_principal`. Мы используем этот идентификатор позднее в этой статье.
    - **Описание**: необязательный, но рекомендуемый параметр.
    
### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Установка Maven и расширения spring-cloud для Az CLI

Этот пример конвейера использует Maven для сборки и Az CLI для развертывания в экземпляре службы. При установке Jenkins создается учетная запись администратора с именем *jenkins*. Убедитесь, что у пользователя *jenkins* есть права на выполнение расширения spring-cloud.

1. Установите SSH-подключение к контроллеру Jenkins.

1. Установите Maven.

    ```bash
    sudo apt-get install maven
    ```

1. Убедитесь, что инструмент Azure CLI установлен, выполнив команду `az version`. Если Azure CLI не установлен, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Переключитесь на пользователя `jenkins`.

    ```bash
    sudo su jenkins
    ```

1. Добавьте расширение **spring-cloud**.

    ```bash
    az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Создание Jenkinsfile

1. В собственном репозитории (https://github.com/&lt ;идентификатор в GitHub&gt; /piggymetrics) создайте файл **Jenkinsfile** в корневой папке.

1. Измените файл указанным ниже образом. Обязательно замените значения **\<resource group name>** и **\<service name>** . Замените заполнитель **azure_service_principal** (субъект-служба Azure) правильным идентификатором, если это значение отличается от того, которое вы указали при добавлении учетных данных в Jenkins.

   ```groovy
       node {
         stage('init') {
           checkout scm
         }
         stage('build') {
           sh 'mvn clean package'
         }
         stage('deploy') {
           withCredentials([azureServicePrincipal('azure_service_principal')]) {
             // login to Azure
             sh '''
               az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
               az account set -s $AZURE_SUBSCRIPTION_ID
             '''  
             // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
             sh 'az configure --defaults group=<resource group name>'
             sh 'az configure --defaults spring-cloud=<service name>'
             // Deploy applications
             sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
             sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
             sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
             sh 'az logout'
           }
         }
       }
   ```

1. Сохраните и зафиксируйте изменения.

## <a name="create-the-job"></a>Создание задания

1. На панели мониторинга Jenkins щелкните **New Item** (Создать элемент).

1. Укажите имя задания *Deploy-PiggyMetrics* и выберите **Pipeline** (Конвейер). Щелкните ОК.

1. Теперь щелкните вкладку **Pipeline** (Конвейер).

1. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).

1. Для параметра **SCM** выберите значение **Git**.

1. Введите URL-адрес GitHub для своей вилки репозитория: **https://github.com/&lt ;идентификатор в GitHub&gt; /piggymetrics.git**

1. Убедитесь, что для параметра **Branch Specifier (black for 'any')** (Описатель ветви) указано значение **_/Azure_*.

1. Для параметра **Script path** (Путь к скрипту) сохраните значение **Jenkinsfile**.

1. Щелкните **Сохранить**.

## <a name="validate-and-run-the-job"></a>Проверка и запуск задания

Перед выполнением задания давайте укажем для поля, где вводится имя для входа, текстовую метку **Введите идентификатор для входа**.

1. Откройте в своем репозитории объект `index.html` из папки **/gateway/src/main/resources/static/** .

1. Выполните поиск по строке "enter your login" и укажите вместо него "Введите идентификатор для входа".

    ```HTML
    <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

1. Фиксация изменений

1. Вручную запустите задание в Jenkins. На панели мониторинга Jenkins щелкните задание *Deploy-PiggyMetrics* а затем **Build Now** (Скомпилировать сейчас).

После выполнения задания откройте общедоступный IP-адрес приложения **шлюза** и убедитесь, что оно успешно обновлено. 

![Обновленное приложение Piggy Metrics](./media/deploy-to-azure-spring-cloud-using-azure-cli/piggymetrics.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы, созданные в рамках этой статьи, если они вам больше не нужны.

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins/)