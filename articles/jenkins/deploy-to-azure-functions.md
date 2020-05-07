---
title: Руководство. Развертывание в Функциях Azure с помощью Jenkins
description: Узнайте, как выполнить развертывание в Функциях Azure с помощью подключаемого модуля Jenkins для Функций Azure.
keywords: jenkins, azure, devops, java, azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 4dbd37b9527a3c41c6f1d5b40edf974b6adda6c3
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82169800"
---
# <a name="tutorial-deploy-to-azure-functions-using-jenkins"></a>Руководство по развертыванию в Функциях Azure с помощью Jenkins

[Функции Azure](/azure/azure-functions/) — это независимая от сервера служба вычислений. С помощью Функций Azure вы можете выполнить код по требованию, не запуская операции по подготовке инфраструктуры или управлению ею. В этом руководстве описано, как развернуть функцию Java в Функциях Azure с помощью подключаемого модуля для Функций Azure.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**: Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Сервер Jenkins**. Если у вас не установлен сервер Jenkins, см. статью о [создании сервера Jenkins в Azure](./configure-on-linux-vm.md).

  > [!TIP]
  > Используемый в этом руководстве исходный код расположен в [репозитории GitHub для Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Создание функции Java

Чтобы создать функцию Java с помощью стека среды выполнения Java, используйте [портал Azure](https://portal.azure.com) или [Azure CLI](/cli/azure/?view=azure-cli-latest).

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
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Подготовка сервера Jenkins

Ниже описывается, как подготовить сервер Jenkins:

1. Разверните [сервер Jenkins](https://aka.ms/jenkins-on-azure) в Azure. Если у вас еще не установлен экземпляр сервера Jenkins, в статье [Создание сервера Jenkins на виртуальной машине Azure под управлением Linux на портале Azure](./configure-on-linux-vm.md) приведены соответствующие пошаговые инструкции.

1. Войдите в экземпляр Jenkins по SSH.

1. Установите на экземпляре Jenkins средство Maven с помощью следующей команды:

    ```terminal
    sudo apt install -y maven
    ```

1. Установите [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) на экземпляре Jenkins. Для этого введите следующие команды в строке терминала:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Установите следующие подключаемые модули на панели мониторинга Jenkins:

    - подключаемый модуль для Функций Azure;
    - подключаемый модуль EnvInject.

1. Серверу Jenkins требуется субъект-служба Azure для аутентификации и доступа к ресурсам Azure. Пошаговые инструкции см. в статье [Руководство. Развертывание из GitHub в Службе приложений Azure с использованием непрерывной интеграции и непрерывного развертывания Jenkins](./deploy-from-github-to-azure-app-service.md).

1. Используя субъект-службу Azure, добавьте на сервер Jenkins тип учетных данных "Microsoft Azure Service Principal" (Субъект-служба Microsoft Azure). Дополнительные сведения см. в [указанной выше статье](./deploy-from-github-to-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="fork-the-sample-github-repo"></a>Создание вилки для примера из репозитория GitHub

1. [Выполните вход в репозиторий GitHub с примером приложения для четных или нечетных чисел](https://github.com/VSChina/odd-or-even-function.git).

1. В правом верхнем углу в GitHub выберите **Fork** (Создать вилку).

1. Следуя инструкциям на экране, выберите свою учетную запись GitHub и завершите создание вилки.

## <a name="create-a-jenkins-pipeline"></a>Создание конвейера Jenkins

В этом разделе описано, как создать [конвейер Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Создайте конвейер на панели мониторинга Jenkins.

1. Установите флажок **Prepare an environment for the run** (Подготовить среду к запуску).

1. Добавьте следующие переменные среды в поле **Properties Content** (Содержимое свойств), заменив заполнители соответствующими значениями для своей среды:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. В разделе **Pipeline->Definition** (Конвейер->Определение) выберите **Pipeline script from SCM** (Скрипт конвейера из SCM).

1. Введите URL-адрес и путь к скрипту (doc/resources/jenkins/JenkinsFile) для своей вилки с GitHub, чтобы использовать их в [ примере JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
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