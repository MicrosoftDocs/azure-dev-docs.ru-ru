---
title: Руководство. Развертывание в Функциях Azure с помощью Jenkins
description: Узнайте, как выполнить развертывание в Функциях Azure с помощью подключаемого модуля Jenkins для Функций Azure.
keywords: jenkins, azure, devops, java, azure functions
ms.topic: tutorial
ms.date: 01/11/2021
ms.custom: devx-track-jenkins,devx-track-cli
ms.openlocfilehash: b2c616dd6d0a2a659a25527072358e8f1095e414
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561060"
---
# <a name="tutorial-deploy-to-azure-functions-using-jenkins"></a>Руководство по развертыванию в Функциях Azure с помощью Jenkins

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

[Функции Azure](/azure/azure-functions/) — это независимая от сервера служба вычислений. С помощью Функций Azure вы можете выполнить код по требованию, не запуская операции по подготовке инфраструктуры или управлению ею. В этом руководстве описано, как развернуть функцию Java в Функциях Azure с помощью подключаемого модуля для Функций Azure.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**: Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Сервер Jenkins**. Если у вас не установлен сервер Jenkins, см. статью о [создании сервера Jenkins в Azure](./configure-on-linux-vm.md).

## <a name="view-the-source-code"></a>Просмотр исходного кода

Используемый в этом руководстве исходный код расположен в [репозитории GitHub для Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Создание функции Java

Чтобы создать функцию Java с помощью стека среды выполнения Java, используйте [портал Azure](https://portal.azure.com) или [Azure CLI](/cli/azure/).

Ниже объясняется, как создать функцию Java с помощью Azure CLI:

1. Создайте группу ресурсов, заменив заполнитель **&lt;resource_group>** именем вашей группы ресурсов.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Создайте учетную запись хранения Azure, заменив заполнители соответствующими значениями.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Создайте приложение-функцию для тестирования, заменив заполнители соответствующими значениями.

    ```azurecli
    az functionapp create --resource-group <resource_group> --runtime java --consumption-plan-location eastus --name <function_app> --storage-account <storage_account> --functions-version 2
    ```

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

Ниже описывается, как подготовить сервер Jenkins:

1. Разверните [сервер Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins) в Azure. Если у вас еще не установлен экземпляр сервера Jenkins, в статье [Создание сервера Jenkins на виртуальной машине Azure под управлением Linux на портале Azure](./configure-on-linux-vm.md) приведены соответствующие пошаговые инструкции.

1. Войдите в экземпляр Jenkins по SSH.

1. На экземпляре Jenkins установите Az CLI 2.0.67 или более поздней версии.

1. Установите Maven с помощью следующей команды:

    ```bash
    sudo apt install -y maven
    ```

1. Установите [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) на экземпляре Jenkins. Для этого введите следующие команды в строке терминала:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    cat /etc/apt/sources.list.d/dotnetdev.list
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

1. Серверу Jenkins требуется субъект-служба Azure для аутентификации и доступа к ресурсам Azure. Пошаговые инструкции см. в статье [Руководство. Развертывание из GitHub в Службе приложений Azure с использованием непрерывной интеграции и непрерывного развертывания Jenkins](./deploy-to-azure-app-service-using-azure-cli.md).

1. Убедитесь, что [подключаемый модуль учетных данных](https://plugins.jenkins.io/credentials/) установлен.

    1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

    1. В разделе **System Configuration** (Конфигурация системы) выберите **Manage Plugins** (Управление подключаемыми модулями).

    1. Откройте вкладку **Установленные**.

    1. В поле **Filter** (Фильтр) введите `credentials`.
    
    1. Убедитесь, что **подключаемый модуль учетных данных** установлен. В противном случае установите его, перейдя на вкладку **Available** (Доступно).

    ![Необходимо установить подключаемый модуль учетных данных.](./media/deploy-to-azure-functions/credentials-plugin.png)

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. В разделе **Security** (Безопасность) выберите **Manage Credentials** (Управление учетными данными).

1. В разделе **Credentials** (Учетные данные) выберите **(global)** (Глобальные).

1. В меню выберите пункт **Add Credentials** (Добавить учетные данные).

1. Введите следующие значения для [субъекта-службы Microsoft Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json):

    - **Вид**. Убедитесь, что тип — **_Имя пользователя с паролем_* _.
    - _*Имя пользователя**: **_идентификатор приложения_*_ созданного субъекта-службы.
    - _*Пароль**: **_пароль_*_ созданного субъекта-службы.
    - _*Идентификатор**: идентификатор учетных данных, например `as azuresp`.

1. Нажмите кнопку **ОК**.

## <a name="fork-the-sample-github-repo"></a>Создание вилки для примера из репозитория GitHub

1. [Выполните вход в репозиторий GitHub с примером приложения для четных или нечетных чисел](https://github.com/VSChina/odd-or-even-function.git).

1. В правом верхнем углу в GitHub выберите **Fork** (Создать вилку).

1. Следуя инструкциям на экране, выберите свою учетную запись GitHub и завершите создание вилки.

## <a name="create-a-jenkins-pipeline"></a>Создание конвейера Jenkins

В этом разделе описано, как создать [конвейер Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Создайте конвейер на панели мониторинга Jenkins.

1. Установите флажок **Prepare an environment for the run** (Подготовить среду к запуску).

1. В разделе **Pipeline->Definition** (Конвейер->Определение) выберите **Pipeline script from SCM** (Скрипт конвейера из SCM).

1. Введите URL-адрес и путь к скрипту (doc/resources/jenkins/JenkinsFile) для своей вилки с GitHub, чтобы использовать их в [ примере JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```nodejs
    node {
    withEnv(['AZURE_SUBSCRIPTION_ID=99999999-9999-9999-9999-999999999999',
            'AZURE_TENANT_ID=99999999-9999-9999-9999-999999999999']) {
        stage('Init') {
            cleanWs()
            checkout scm
        }

        stage('Build') {
            sh 'mvn clean package'
        }

        stage('Publish') {
            def RESOURCE_GROUP = '<resource_group>' 
            def FUNC_NAME = '<function_app>'
            // login Azure
            withCredentials([usernamePassword(credentialsId: 'azuresp', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
            sh '''
                az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                az account set -s $AZURE_SUBSCRIPTION_ID
            '''
            }
            sh 'cd $PWD/target/azure-functions/odd-or-even-function-sample && zip -r ../../../archive.zip ./* && cd -'
            sh "az functionapp deployment source config-zip -g $RESOURCE_GROUP -n $FUNC_NAME --src archive.zip"
            sh 'az logout'
            }
        }
    }
    ```

## <a name="build-and-deploy"></a>Сборка и развертывание

Теперь можно выполнить задание Jenkins.

1. Сначала получите ключ авторизации, выполнив инструкции из статьи [Триггеры и привязки HTTP в службе "Функции Azure"](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys).

1. Введите URL-адрес приложения в окне браузера. Замените заполнители соответствующими значениями и укажите числовое значение для заполнителя **&lt;input_number>** в качестве ввода для функции Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Отобразятся результаты, аналогичные приведенным ниже в качестве примера выходным данным (для тестирования использовалось нечетное число 365):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданные ресурсы, выполнив следующее действие:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Функции Azure](/azure/azure-functions/)