---
title: Использование начального приложения Spring Boot с API службы хранилища Azure
description: Сведения о настройке приложения Spring Boot Initializer с помощью API службы хранилища Azure.
services: storage
documentationcenter: java
ms.date: 12/19/2018
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage
ms.openlocfilehash: ef39aa09fb3f10f34d70fb5b0a72cd8a55f0859e
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81669130"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-storage-api"></a>Использование начального приложения Spring Boot с API службы хранилища Azure

## <a name="overview"></a>Обзор

В этой статье описано, как с помощью **Spring Initializr** создать приложение для получения доступа к хранилищу Azure с помощью API службы хранилища Azure.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться для получения [бесплатной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Интерфейс командной строки Azure (CLI)](/cli/azure/overview).
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 и выше.

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Создание пользовательского приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что необходимо создать проект **Maven** с помощью **Java**, введите имя **группы** и **артефакта** вашего приложения, а затем щелкните ссылку, чтобы **перейти к полной версии** Spring Initializr.

   ![Основные параметры Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-basic.png)

   > [!NOTE]
   >
   > Spring Initializr будет использовать имена **группы** и **артефакта** для создания имени пакета, например *com.contoso.wingtiptoysdemo*.
   >

1. Прокрутите вниз до раздела **Azure** статьи и установите флажок рядом с пунктом **Azure Storage** (Служба хранилища Azure).

   ![Все параметры Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-advanced.png)

1. Прокрутите страницу вниз и нажмите соответствующую кнопку, чтобы **создать проект**.

   ![Все параметры Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-generate.png)

1. При появлении запроса скачайте проект на локальный компьютер.

   ![Скачивание пользовательского проекта Spring Boot](media/configure-spring-boot-starter-java-app-with-azure-storage/download-app.png)

## <a name="sign-into-azure-and-select-the-subscription-to-use"></a>Вход в Azure и выбор подписки для использования

1. Откройте командную строку.

1. Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.

   ```azurecli
   az login
   ```
   Для завершения процесса входа следуйте инструкциям.

1. Отобразите список подписок:

   ```azurecli
   az account list
   ```
   Azure отобразит список подписок, и вам нужно будет скопировать идентификатор GUID для подписки, которая будет использоваться, например:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Укажите GUID учетной записи, которую вы собираетесь использовать в Azure, например:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

1. Создайте группу ресурсов Azure, используемых в этой статье, например:
   ```azurecli
   az group create --name wingtiptoysresources --location westus
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `name` | Указывает уникальное имя для группы ресурсов. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения группы ресурсов. |

   В Azure CLI отобразятся результаты созданной группы ресурсов, например:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/wingtiptoysresources",
     "location": "westus",
     "managedBy": null,
     "name": "wingtiptoysresources",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Создайте учетную запись хранения Azure в группе ресурсов для приложения Spring Boot, например:
   ```azurecli
   az storage account create --name wingtiptoysstorage --resource-group wingtiptoysresources --location westus --sku Standard_LRS
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `name` | Указывает уникальное имя для учетной записи хранения. |
   | `resource-group` | Указывает имя созданной ранее группы ресурсов. |
   | `location` | Указывает [регион Azure](https://azure.microsoft.com/regions/) для размещения учетной записи хранения. |
   | `sku` | Указывает одно из следующих значений: `Premium_LRS`, `Standard_GRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_ZRS`. |

   Azure вернет длинную строку JSON, которая содержит сведения о состоянии подготовки, например:

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "identity": null,
     "kind": "Storage"
       ...
       ... (A long list of values will be displayed here.)
       ...
     "statusOfPrimary": "available",
     "statusOfSecondary": null,
     "tags": {},
     "type": "Microsoft.Storage/storageAccounts"
   }
   ```

3. Извлеките строку подключения для учетной записи хранения, например:
   ```azurecli
   az storage account show-connection-string --name wingtiptoysstorage --resource-group wingtiptoysresources
   ```
   Где:

   | Параметр | Описание |
   | ---|---|
   | `name` | Указывает уникальное имя созданной ранее учетной записи хранения. |
   | `resource-group` | Указывает имя созданной ранее группы ресурсов. |

   Azure вернет строку JSON, которая содержит строку подключения для учетной записи хранения, например:

   ```json
   {
     "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz=="
   }
   ```

## <a name="configure-and-compile-your-spring-boot-application"></a>Настройка и компиляция приложения Spring Boot

1. Распакуйте скачанный архив с файлами проекта в каталог.

1. В папке *src/main/resources* проекта откройте файл *application.properties* в текстовом редакторе.

1. Добавьте ключ для учетной записи хранения, например:
   ```yaml
   azure.storage.connection-string=DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz==
   ```

1. В папке */src/main/java/com/example/wingtiptoysdemo* проекта откройте файл *WingtiptoysdemoApplication.java* в текстовом редакторе.

1. Замените существующий код Java следующим примером, который содержит список больших двоичных объектов в контейнере:

   ```java
   package com.example.wingtiptoysdemo;

   import com.microsoft.azure.storage.*;
   import com.microsoft.azure.storage.blob.*;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   import java.net.URISyntaxException;

   @SpringBootApplication
   public class WingtiptoysdemoApplication implements CommandLineRunner {

      @Autowired
      private CloudStorageAccount cloudStorageAccount;

      final String containerName = "mycontainer";

      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysdemoApplication.class, args);
      }

      public void run(String... var1)
             throws URISyntaxException, StorageException {
          // Create a container (if it does not exist).
          createContainerIfNotExists(containerName);
          // Upload a blob to the container.
          uploadTextBlob(containerName);
      }

      private void createContainerIfNotExists(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Create the container if it does not exist.
            container.createIfNotExists();
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }

      private void uploadTextBlob(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Get a blob reference for a text file.
            CloudBlockBlob blob = container.getBlockBlobReference("test.txt");
            // Upload some text into the blob.
            blob.uploadText("Hello World!");
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }
   }
   ```
   > [!NOTE]
   >
   > Этот пример автоматически передает параметры учетной записи хранения, определенные в файле *application.properties*.
   >

1. Скомпилируйте и запустите приложение:
   ```shell
   mvn clean package spring-boot:run
   ```

   Приложение создаст контейнер и отправит в контейнер текстовый файл как большой двоичный объект, который появится в списке в вашей учетной записи хранения на [портале Azure](https://portal.azure.com).

   ![Список больших двоичных объектов на портале Azure](media/configure-spring-boot-starter-java-app-with-azure-storage/list-blobs-in-portal.png)

   > [!NOTE]
   > 
   > При компиляции приложения может появиться следующее сообщение об ошибке:
   > 
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] BUILD FAILURE`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] Total time: 2.616 s`<br/>
   > `[INFO] Finished at: 2017-11-11T13:14:15Z`<br/>
   > `[INFO] Final Memory: 26M/213M`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2`<br/>
   > `.18.1:test (default-test) on project wingtiptoysdemo: Execution default-test of`<br/>
   > `goal org.apache.maven.plugins:maven-surefire-plugin:2.18.1:test failed: The for`<br/>
   > `ked VM terminated without properly saying goodbye. VM crash or System.exit called?`<br/>
   > `[ERROR] Command was /bin/sh -c cd /home/robert/SpringBoot/wingtiptoysdemo && /u`<br/>
   > `sr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -jar /home/robert/SpringBoot/wingt`<br/>
   > `iptoysdemo/target/surefire/surefirebooter6371623993063346766.jar /home/robert/S`<br/>
   > `pringBoot/wingtiptoysdemo/target/surefire/surefire5107893623933537917tmp /home/`<br/>
   > `robert/SpringBoot/wingtiptoysdemo/target/surefire/surefire_01414159391084128068tmp`<br/>
   > `[ERROR] -> [Help 1]`<br/>
   > 
   > В таком случае можно отключить тестирование Maven Surefire. Для этого добавьте следующую запись подключаемого модуля в файл *pom.xml*:
   > 
   > ```xml
   > <plugin>
   >   <groupId>org.apache.maven.plugins</groupId>
   >   <artifactId>maven-surefire-plugin</artifactId>
   >   <version>2.20.1</version>
   >   <configuration>
   >     <skipTests>true</skipTests>
   >   </configuration>
   > </plugin>
   > ```
   > 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

См. дополнительные сведения о других [начальных приложениях Spring Boot для Microsoft Azure](spring-boot-starters-for-azure.md).

См. дополнительные сведения об интеграции функций Azure в приложения на основе Spring в руководстве по [Spring Framework в Azure](/azure/developer/java/spring-framework/).

См. дополнительные сведения о других API-интерфейсах службы хранилища Azure, которые можно вызывать из приложений Spring Boot:
* [Использование хранилища BLOB-объектов Azure из Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Использование хранилища очередей Azure из Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Использование хранилища таблиц Azure из Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Использование хранилища файлов Azure из Java](/azure/storage/files/storage-java-how-to-use-file-storage)
