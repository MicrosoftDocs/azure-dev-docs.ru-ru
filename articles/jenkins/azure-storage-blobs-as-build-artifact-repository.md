---
title: Руководство. Использование службы хранилища Azure для артефактов сборки
description: Узнайте, как использовать службы BLOB-объектов Azure в качестве репозитория для артефактов сборки, созданных решением непрерывной интеграции Jenkins.
keywords: Jenkins, Azure, DevOps, хранилище, CI/CD, артефакты сборки
ms.topic: article
ms.date: 01/12/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 31c86da8f861e4295967007cb1b885325feb93dc
ms.sourcegitcommit: 75a1f26aaff48a89631805df4b4a0c006de6a271
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128161"
---
# <a name="tutorial-use-azure-storage-for-build-artifacts"></a>Руководство по использованию службы хранилища Azure для артефактов сборки

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

В этой статье демонстрируется использование хранилища BLOB-объектов в качестве репозитория для артефактов сборки, созданных решением непрерывной интеграции Jenkins, или в качестве источника скачиваемых файлов для процесса сборки. Одним из сценариев, где это может оказаться полезным, является программирование в гибкой среде разработки (с помощью Java или других языков), сборка выполняется на основе непрерывной интеграции, и вам требуется репозиторий для артефактов построения, чтобы можно было, например, использовать их совместно с другими членами организации, вашими клиентами или вести архив. Еще один сценарий связан с ситуацией, когда задание построения само требует других файлов, например, зависимостей для загрузки в качестве входа построения.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**: Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Сервер Jenkins**. Если он у вас не установлен, [создайте сервер Jenkins в Azure](./configure-on-linux-vm.md).
- **Azure CLI.** Установите Azure CLI (версии 2.0.67 или более поздней версии) на сервере Jenkins.
- **Учетная запись хранения Azure.** Если у вас еще нет учетной записи хранения, [создайте ее](/azure/storage/common/storage-account-create).

## <a name="add-azure-credential-needed-to-execute-azure-cli"></a>Для выполнения Azure CLI требуются учетные данные Azure.

1. Перейдите на портал Jenkins.

1. В меню выберите пункт **Manage Jenkins** (Управление Jenkins).

1. Выберите элемент **Manage Credentials** (Управление учетными данными).

1. Выберите **глобальный** домен.

1. Щелкните **Добавление учетных данных**.

1. Заполните обязательные поля:

    - **Вид**. Укажите **имя пользователя и пароль**.
    - **Имя пользователя**. Укажите `appId` субъекта-службы.
    - **Пароль**. Укажите `password` субъекта-службы.
    - **Идентификатор**. Укажите идентификатор учетных данных, например `azuresp`.
    - **Описание.** При необходимости включите понятное описание среды.

1. Нажмите кнопку **ОК**, чтобы создать учетные данные.

## <a name="create-a-pipeline-job-to-upload-build-artifacts"></a>Создание задания конвейера для отправки артефактов сборки

Ниже приведены пошаговые инструкции по созданию задания конвейера. В рамках задания конвейера создается несколько файлов, затем они отправляются в учетную запись хранения с помощью Azure CLI.

1. На панели мониторинга Jenkins выберите элемент **New Item** (Новый элемент).

1. Присвойте заданию имя **myjob**, выберите элемент **Pipeline** (Конвейер) и нажмите кнопку **ОК**.

1. В разделе **Pipeline** (Конвейер) в конфигурации задания выберите элемент **Pipeline script** (Скрипт конвейера) и вставьте приведенный ниже текст в поле **Script** (Скрипт). Измените заполнители, используя значения для своей среды.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            sh 'rm -rf *'
            sh 'mkdir text'
            sh 'echo Hello Azure Storage from Jenkins > ./text/hello.txt'
            sh 'date > ./text/date.txt'
          }
    
          post {
            success {
              withCredentials([usernamePassword(credentialsId: 'azuresp', 
                              passwordVariable: 'AZURE_CLIENT_SECRET', 
                              usernameVariable: 'AZURE_CLIENT_ID')]) {
                sh '''
                  echo $container_name
                  # Login to Azure with ServicePrincipal
                  az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                  # Set default subscription
                  az account set --subscription $AZURE_SUBSCRIPTION_ID
                  # Execute upload to Azure
                  az storage container create --account-name $AZURE_STORAGE_ACCOUNT --name $JOB_NAME --auth-mode login
                  az storage blob upload-batch --destination ${JOB_NAME} --source ./text --auth-mode login
                  # Logout from Azure
                  az logout
                '''
              }
            }
          }
        }
      }
    }
    ```
    
1. Выберите элемент **Build Now** (Выполнить сборку), чтобы запустить **myjob**.

1. Изучите выходные данные в консоли для определения состояния. Когда с помощью действия после сборки отправляются артефакты сборки, сообщения о состоянии для службы хранилища Azure записываются в консоль.

1. Если возникнет ошибка, аналогичная указанной ниже, значит, вам нужно предоставить доступ на уровне контейнера: `ValidationError: You do not have the required permissions needed to perform this operation.` Если вы получили это сообщение об ошибке, см. следующие статьи для устранения проблемы:

    - [Выбор способа авторизации доступа к данным BLOB-объектов с помощью Azure CLI](/azure/storage/blobs/authorize-data-operations-cli)
    - [Назначение роли Azure для доступа к BLOB-объектам и данным очереди с помощью портала Azure](/azure/storage/common/storage-auth-aad-rbac-portal)

1. После успешного завершения задания можно просмотреть артефакты сборки, открыв общедоступный большой двоичный объект:

    1. Войдите на [портал Azure](https://portal.azure.com).
    1. Выберите **Хранилище**.
    1. Щелкните имя учетной записи хранения, используемой для решения Jenkins.
    1. Выберите **Контейнеры**.
    1. Выберите контейнер с именем **myjob** в списке больших двоичных объектов.
    1. Должна отобразиться два файла: **hello.txt** и **date.txt**.
    1. Скопируйте URL-адрес любого из них и откройте его в браузере. 
    1. Отобразится текстовый файл, который был загружен в качестве артефакта сборки.
    
    **Примечания**

    - Имена контейнеров и BLOB-объектов в хранилище Azure отображаются строчными буквами (и с учетом регистра).

## <a name="create-a-pipeline-job-to-download-from-azure-blob-storage"></a>Создание задания конвейера для скачивания элементов из Хранилища BLOB-объектов Azure

В следующих шагах показано, как настроить задание конвейера для скачивания элементов из Хранилища BLOB-объектов Azure.

1. В разделе **Pipeline** (Конвейер) в конфигурации задания выберите элемент **Pipeline script** (Скрипт конвейера) и вставьте приведенный ниже текст в поле **Script** (Скрипт). Измените заполнители, используя значения для своей среды.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            withCredentials([usernamePassword(credentialsId: 'azuresp', 
                            passwordVariable: 'AZURE_CLIENT_SECRET', 
                            usernameVariable: 'AZURE_CLIENT_ID')]) {
              sh '''
                # Login to Azure with ServicePrincipal
                az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                # Set default subscription
                az account set --subscription $AZURE_SUBSCRIPTION_ID
                # Execute upload to Azure
                az storage blob download --account-name $AZURE_STORAGE_ACCOUNT --container-name myjob --name hello.txt --file ${WORKSPACE}/hello.txt --auth-mode login
                # Logout from Azure
                az logout
              '''   
            }
          }
        }
      }
    }
    ```
    
1. После сборки проверьте выходные данные консоли журнала сборки. Кроме того, можно просмотреть расположение для скачивания, чтобы узнать, были ли успешно скачаны нужные большие двоичные объекты.  

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/Jenkins/)