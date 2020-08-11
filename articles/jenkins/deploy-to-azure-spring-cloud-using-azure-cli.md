---
title: Развертывание приложений в Azure Spring Cloud с помощью Jenkins и Azure CLI
description: Узнайте, как развертывать микрослужбы в службе Azure Spring Cloud с помощью Azure CLI в конвейере непрерывной интеграции и развертывания
keywords: Jenkins, Azure, DevOps, Azure Spring Cloud, Azure CLI
ms.topic: tutorial
ms.date: 01/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0135dbfa67f1de2b0602ae06b7ab970c2b560d59
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87478314"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Руководство по Развертывание приложений в Azure Spring Cloud с помощью Jenkins и Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) — это полностью управляемая среда для разработки микрослужб со встроенными функциями обнаружения служб и управления конфигурацией. Эта служба позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot. В этом руководстве показано, как использовать Azure CLI в Jenkins для автоматизации непрерывной интеграции и доставки (CI/CD) в Azure Spring Cloud.

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * подготовка экземпляра службы и запуск приложения Java Spring;
> * подготовка сервера Jenkins;
> * создание и развертывание приложений микрослужб с использованием Azure CLI в конвейере Jenkins. 

В этом руководстве предполагается, что у вас есть по меньшей мере средний уровень знаний о службах Azure, Azure Spring Cloud, [конвейерах](https://jenkins.io/doc/book/pipeline/), подключаемых модулей Jenkins и репозитории GitHub.

## <a name="prerequisites"></a>Предварительные требования

>[!Note]
> Сейчас предоставляется общедоступная предварительная версия Azure Spring Cloud. Предложения общедоступной предварительной версии позволяют клиентам экспериментировать с новыми функциями до официального выпуска.  Общедоступные предварительные версии функций и служб не предназначены для использования в рабочей среде.  Чтобы получить дополнительные сведения о поддержке на этапе использования предварительных версий, ознакомьтесь с разделом [Вопросы и ответы](https://azure.microsoft.com/support/faq/) или оформите [запрос на поддержку](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

- **Подписка Azure**: Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

* Учетная запись GitHub. Если у вас нет учетной записи GitHub, [создайте бесплатную учетную запись](https://github.com/), прежде чем начинать работу.

* Главный узел Jenkins. Если у вас еще нет главного узла Jenkins, разверните [Jenkins](https://aka.ms/jenkins-on-azure) в Azure, выполнив инструкции из  [этого краткого руководства](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Для узла или агента Jenkins требуются следующие компоненты (например, для сервера сборки):

    * [Git](https://git-scm.com/);
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
    * [Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi);
    * [установленное средство Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.67 или выше.

    >[!TIP]
    > Такие средства, как Git, JDK, Az CLI и подключаемый модуль Azure, по умолчанию включаются в шаблон решения [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) в Azure Marketplace.
    
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Подготовка экземпляра службы и запуск приложения Java Spring

В качестве примера приложения для службы Майкрософт мы используем [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics), для которого выполняем те же инструкции, что и в рамках [краткого руководства по запуску приложения Java Spring с помощью Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli), для инициализации экземпляра службы и настройки приложений. Если вы уже сделали это, можете пропустить следующий раздел. Если нет, выполните команды Azure CLI, указанные далее. В [кратком руководстве по запуску приложения Java Spring с помощью Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) вы найдете дополнительные сведения на эту тему.

Локальный компьютер должен соответствовать тем же предварительным требованиям, что и сервер сборки Jenkins. Чтобы выполнять сборку и развертывание приложений микрослужб, убедитесь, что установлены следующие компоненты:
    * [Git](https://git-scm.com/);
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
    * [Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi);
    * [установленное средство Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.67 или выше.

1. Установите расширение Azure Spring Cloud.

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Создайте группу ресурсов, которая будет содержать службу Azure Spring Cloud.

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Подготовьте к работе экземпляр Azure Spring Cloud.

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Создайте вилку репозитория [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) в своей учетной записи GitHub. На локальном компьютере клонируйте репозиторий в каталог с именем `source-code`.

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Настройте сервер конфигурации. Не забудьте заменить заполнитель &lt;your GitHub id&gt; реальным значением идентификатора GitHub.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Скомпилируйте проект.

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Создайте три микрослужбы: **gateway**, **auth-service** и **account-service**.

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Разверните приложение. 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Назначьте шлюзу общедоступную конечную точку.

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Отправьте приложению шлюза запрос на получение URL-адреса и убедитесь, что приложение успешно работает.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Перейдите по URL-адресу, полученному с помощью предыдущей команды, чтобы запустить приложение PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

В рамках этого раздела вы подготовите к сборке сервер Jenkins, что вполне пригодно для тестирования. Но для сборки реальных приложений по соображениям безопасности следует развернуть в Azure [агент виртуальной машины Azure](https://plugins.jenkins.io/azure-vm-agents) или [агент контейнеров Azure](https://plugins.jenkins.io/azure-container-agents). Дополнительные сведения см. в статье о [вопросах безопасности при сборке в главном узле](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) в документации по Jenkins.

### <a name="install-plug-ins"></a>Установка подключаемых модулей

1. Выполните вход на сервер Jenkins. Выберите действие **Manage Jenkins (Управление Jenkins) > Manage Plugins (Управление подключаемыми модулями)** .
2. На вкладке **Available** (Доступные) выберите такие подключаемые модули:
    * [интеграция с GitHub](https://plugins.jenkins.io/github-pullrequest);
    * [учетные данные Azure](https://plugins.jenkins.io/azure-credentials).

    Если этих подключаемых модулей нет в списке, перейдите на вкладку **Installed** (Установленные) и проверьте, установлены ли они.

3. Чтобы установить подключаемые модули, выберите **Download now and install after restart** (Скачать сейчас и установить после перезагрузки).

4. Чтобы завершить установку, перезагрузите сервер Jenkins.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Добавление учетных данных субъекта-службы Azure в хранилище учетных данных Jenkins

1. Для развертывания в Azure вам нужен субъект-служба Azure. Дополнительные сведения см. в  [этом разделе](deploy-from-github-to-azure-app-service.md#create-service-principal) руководства по развертыванию в Службе приложений Azure. Результат `az ad sp create-for-rbac` должен выглядеть следующим образом:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. На панели мониторинга Jenkins выберите **Credentials** (Учетные данные) > **System** (Система). Затем выберите **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).

3. Щелкните **Добавление учетных данных**. 

4. Выберите тип учетных данных **Субъект-служба Microsoft Azure**.

5. Укажите следующие значения: * Subscription ID: идентификатор вашей подписки Azure * Client ID: укажите `appId` * Client Secret: укажите `password` * Tenant ID: укажите `tenant` * Azure Environment: выберите предварительно настроенное значение. Например, **Azure** для среды Azure Global * ID: укажите **azure_service_principal**. Этот идентификатор мы применим позднее в нашей статье * Description: необязательное поле. Рекомендуем указать в нем содержательное значение.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Установка Maven и расширения spring-cloud для Az CLI

Этот пример конвейера использует Maven для сборки и Az CLI для развертывания в экземпляре службы. При установке Jenkins создается учетная запись администратора с именем *jenkins*. Убедитесь, что у пользователя *jenkins* есть права на выполнение расширения spring-cloud.

1. Установите SSH-подключение к главному узлу Jenkins. 

2. Установка Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Установите Azure CLI. Дополнительные сведения см. в статье об [установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI устанавливается по умолчанию, если вы используете [мастер Jenkins в Azure](https://aka.ms/jenkins-on-azure).

4. Переключитесь на пользователя `jenkins`.

    ```bash
        sudo su jenkins
    ```

5. Добавьте расширение **spring-cloud**.

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Создание Jenkinsfile
1. В собственном репозитории (https://github.com/&lt ;идентификатор в GitHub&gt; /piggymetrics) создайте файл **Jenkinsfile** в корневой папке.

2. Измените файл указанным ниже образом. Обязательно замените значения **\<resource group name>** и **\<service name>** . Замените заполнитель **azure_service_principal** (субъект-служба Azure) правильным идентификатором, если это значение отличается от того, которое вы указали при добавлении учетных данных в Jenkins. 

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

3. Сохраните и зафиксируйте изменения.

## <a name="create-the-job"></a>Создание задания

1. На панели мониторинга Jenkins щелкните **New Item** (Создать элемент).

2. Укажите имя задания *Deploy-PiggyMetrics* и выберите **Pipeline** (Конвейер). Щелкните ОК.

3. Теперь щелкните вкладку **Pipeline** (Конвейер).

4. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).

5. Для параметра **SCM** выберите значение **Git**.

6. Введите URL-адрес GitHub для своей вилки репозитория: **https://github.com/&lt ;идентификатор в GitHub&gt; /piggymetrics.git**

7. Убедитесь, что для параметра **Branch Specifier (black for 'any')** (Описатель ветви) указано значение * **/Azure**.

8. Для параметра **Script path** (Путь к скрипту) сохраните значение **Jenkinsfile**.

7. Щелкните **Сохранить**.

## <a name="validate-and-run-the-job"></a>Проверка и запуск задания

Перед выполнением задания давайте укажем для поля, где вводится имя для входа, текстовую метку **Введите идентификатор для входа**.

1. Откройте в своем репозитории объект `index.html` из папки **/gateway/src/main/resources/static/** .

2. Выполните поиск по строке "enter your login" и укажите вместо него "Введите идентификатор для входа".

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Фиксация изменений

4. Вручную запустите задание в Jenkins. На панели мониторинга Jenkins щелкните задание *Deploy-PiggyMetrics* а затем **Build Now** (Скомпилировать сейчас).

После выполнения задания откройте общедоступный IP-адрес приложения **шлюза** и убедитесь, что оно успешно обновлено. 

![Обновленное приложение Piggy Metrics](./media/deploy-to-azure-spring-cloud-using-azure-cli/piggymetrics.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы, созданные в рамках этой статьи, если они вам больше не нужны.

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как использовать Azure CLI в Jenkins для автоматизации непрерывной интеграции и доставки (CI/CD) в Azure Spring Cloud.

Дополнительные сведения о поставщике Jenkins в Azure, см. в разделе Jenkins на сайте Azure.

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins/)
