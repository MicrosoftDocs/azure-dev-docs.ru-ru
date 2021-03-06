---
title: Как использовать Spring Boot Starter со службой хранилища Azure
description: Сведения о настройке приложения Spring Initializr с помощью начального приложения службы хранилища Azure.
services: storage
documentationcenter: java
ms.date: 10/14/2020
ms.service: storage
ms.topic: article
ms.workload: storage
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7e6437859b7b5e67edd9386d0391535dd6537ff7
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442124"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-storage"></a>Как использовать Spring Boot Starter со службой хранилища Azure

В статье описано, как создать пользовательское приложение с помощью **Spring Initializr** и добавить к нему начальное приложение службы хранилища Azure, а затем отправить большой двоичный объект в учетную запись хранения Azure с помощью этого приложения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться для получения [бесплатной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Интерфейс командной строки Azure (CLI)](/cli/azure/index).
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

> [!IMPORTANT]
>
> Для выполнения операций, описанных в этой статье, требуется Spring Boot 2.0 или более поздней версии.
>

## <a name="create-an-azure-storage-account-and-blob-container-for-your-application"></a>Создание учетной записи хранения Azure и контейнера BLOB-объектов для приложения

Следующая процедура создает учетную запись хранения и контейнер на портале.

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Выберите **Создать ресурс**, **Начало работы** и **Учетная запись хранения**.

   ![Создание учетной записи хранения Azure][IMG01]

1. На странице **Создание учетной записи хранения** введите следующие сведения:

   * Выберите **Подписка**.
   * Выберите **группу ресурсов** или создайте новую.
   * Введите уникальное **имя учетной записи хранения**, которое станет частью URI для этой учетной записи хранения. Например, если вы зададите **wingtiptoysstorage** в качестве **имени**, URI примет вид *wingtiptoysstorage.core.windows.net*.
   * Укажите **расположение** для учетной записи хранения.
1. Указав эти параметры, щелкните **Просмотр и создание**. 
1. Проверьте спецификацию и щелкните **Создать**, чтобы создать учетную запись хранения.
1. Когда развертывание будет завершено, выберите **Перейти ресурсу**.
1. Выберите **Контейнеры**.
1. Выберите **Контейнер**.
   * Присвойте контейнеру имя,
   * Выберите *Большой двоичный объект* в раскрывающемся списке.

   ![Создание контейнера больших двоичных объектов][IMG02]

1. Созданный контейнер BLOB-объектов отобразится в соответствующем списке на портале Azure.

Вы также можете использовать Azure CLI для создания учетной записи хранения Azure и контейнера, выполнив следующие действия. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

1. Откройте командную строку.
1. Вход в учетную запись Azure:

   ```azurecli
   az login
   ```
   
1. Если у вас нет группы ресурсов, создайте ее, выполнив следующую команду:
   
   ```azurecli
   az group create \
      --name <resource-group> \
      --location <location>
   ```
   
1. Создайте учетную запись хранения, выполнив следующую команду:
  
   ```azurecli
    az storage account create \
      --name <storage-account> \
      --resource-group <resource-group> \
      --location <location> 
   ```

1. Чтобы создать контейнер, используйте следующую команду:
   
   ```azurecli
    az storage container create \
      --account-name <storage-account-name> \
      --name <container-name> \
      --auth-mode login
   ```
## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

Следующая процедура создает приложение Spring Boot.

1. Перейдите по адресу <https://start.spring.io/>.

1. Задайте такие параметры:

   * Создайте проект **Maven**.
   * Укажите **Java 11**.
   * Укажите версию **Spring Boot** не ниже 2.3.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Добавление зависимости **Spring Web**.

      ![Основные параметры Spring Initializr][SI01]

   > [!NOTE]
   > Spring Initializr использует имена **Group** (Группы) и **Artifact** (Артефакта) для создания имени пакета, как например *com.wingtiptoys.storage*.

1. Указав перечисленные выше параметры, щелкните **GENERATE** (Создать).

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать.

## <a name="configure-your-spring-boot-app-to-use-the-azure-storage-starter"></a>Настройка приложения Spring Boot для использования начального приложения службы хранилища Azure

Следующая процедура настраивает хранилище Azure для приложения Spring Boot.

1. Найдите файл *pom.xml* в корневой папке приложения, например так:

   `C:\SpringBoot\storage\pom.xml`

   -или-

   `/users/example/home/storage/pom.xml`

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud для службы хранилища Azure в список `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-starter-azure-storage</artifactId>
      <version>1.2.8</version>
   </dependency>
   ```

1. Если вы используете JDK 9 или более поздней версии, добавьте следующие зависимости:

   ```xml
   <dependency>
       <groupId>javax.xml.bind</groupId>
       <artifactId>jaxb-api</artifactId>
       <version>2.3.1</version>
   </dependency>
   <dependency>
       <groupId>org.glassfish.jaxb</groupId>
       <artifactId>jaxb-runtime</artifactId>
       <version>2.3.1</version>
       <scope>runtime</scope>
   </dependency>
   ```

1. Сохраните и закройте файл *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Создание файла учетных данных Azure

Следующая процедура создает файл учетных данных Azure.

1. Откройте командную строку.

1. Перейдите к каталогу *resources* приложения Spring Boot, например так:

   ```cmd
   cd C:\SpringBoot\storage\src\main\resources
   ```

   -или-

   ```bash
   cd /users/example/home/storage/src/main/resources
   ```

1. Вход в учетную запись Azure:

   ```azurecli
   az login
   ```

1. Отобразите список подписок:

   ```azurecli
   az account list
   ```
   Azure отобразит список подписок, и вам нужно будет скопировать идентификатор GUID для подписки, которая будет использоваться, например:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Укажите GUID подписки, которую вы собираетесь использовать в Azure, например:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Создайте файл учетных данных Azure:

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   Эта команда создаст файл *my.azureauth* в каталоге *resources* приблизительно с таким содержимым:

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-storage-account"></a>Настройка приложения Spring Boot для использования учетной записи хранения Azure

Следующая процедура настраивает учетную запись хранения Azure для приложения Spring Boot.

1. Найдите файл *application.properties* в каталоге *resources* приложения, например так:

   `C:\SpringBoot\storage\src\main\resources\application.properties`

   -или-

   `/users/example/home/storage/src/main/resources/application.properties`

2. Откройте файл *application.properties* в текстовом редакторе, добавьте указанные ниже строки в файл и замените примеры значений соответствующими параметрами учетной записи хранения:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=westUS
   spring.cloud.azure.storage.account=wingtiptoysstorage
   blob=azure-blob://containerName/blobName
   ```
   Где:

   |                   Поле                   |                                            Описание                                            |
   |-------------------------------------------|---------------------------------------------------------------------------------------------------|
   | `spring.cloud.azure.credential-file-path` |            Определяет файл учетных данных Azure, который был создан ранее в этом примере.             |
   |    `spring.cloud.azure.resource-group`    |           Определяет группу ресурсов Azure, которая содержит учетную запись хранения Azure.            |
   |        `spring.cloud.azure.region`        | Определяет географический регион, указанный при создании учетной записи хранения Azure. |
   |   `spring.cloud.azure.storage.account`    |            Определяет учетную запись хранения Azure, которая была создана ранее в этом примере.             |
   |                   `blob`                  |           Определяет имена контейнера и большого двоичного объекта для хранения данных.         |
    
3. Сохраните и закройте файл *application.properties*.

## <a name="add-sample-code-to-implement-basic-azure-storage-functionality"></a>Добавление примера кода для реализации базовых функций службы хранилища Azure

В этом разделе вы создадите классы Java, требуемые для хранения большого двоичного объекта в учетной записи хранения Azure.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\StorageApplication.java`

   -или-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/StorageApplication.java`

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки. Замените wingtiptoys реальными значениями:

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class StorageApplication {
      public static void main(String[] args) {
         SpringApplication.run(StorageApplication.class, args);
      }
   }
   ```

1. Сохраните и закройте файл основного приложения Java.

### <a name="add-a-blob-controller-class"></a>Добавление класса контроллера большого двоичного объекта

1. Создайте файл Java с именем *BlobController.java* в каталоге пакета приложения, например так:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\BlobController.java`

   -или-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/BlobController.java`

1. Откройте файл Java контроллера большого двоичного объекта в текстовом редакторе и добавьте указанные ниже строки.  Вместо *wingtiptoys* укажите реальную группу ресурсов, а вместо *storage* — реальное имя артефакта.

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.WritableResource;
   import org.springframework.util.StreamUtils;
   import org.springframework.web.bind.annotation.*;

   import java.io.IOException;
   import java.io.OutputStream;
   import java.nio.charset.Charset;

   @RestController
   @RequestMapping("blob")
   public class BlobController {
   
       @Value("${blob}")
       private Resource blobFile;
   
       @GetMapping
       public String readBlobFile() throws IOException {
           return StreamUtils.copyToString(
                   this.blobFile.getInputStream(),
                   Charset.defaultCharset());
       }
   
       @PostMapping
       public String writeBlobFile(@RequestBody String data) throws IOException {
           try (OutputStream os = ((WritableResource) this.blobFile).getOutputStream()) {
               os.write(data.getBytes());
           }
           return "file was updated";
       }
   }
   ```

1. Сохраните и закройте файл Java контроллера большого двоичного объекта.

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*, например:

   ```cmd
   cd C:\SpringBoot\storage
   ```

   -или-
   
   ```bash
   cd /users/example/home/storage
   ```

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. После запуска приложения можно использовать средство *curl*, чтобы протестировать приложение, например:

   а. Отправьте запрос POST, чтобы обновить содержимое файла:

      ```shell
      curl -d 'new message' -H 'Content-Type: text/plain' localhost:8080/blob
      ```

      Вы должны получить ответ, подтверждающий обновление файла.

   b. Отправьте запрос GET, чтобы проверить содержимое файла:

      ```shell
      curl -X GET http://localhost:8080/
      ```

     Должен отобразиться отправленный вами текст "Hello World".

## <a name="summary"></a>Сводка

В этом руководстве описано, как создать приложение Java с помощью **[Spring Initializr]** и добавить к нему начальное приложение службы хранилища Azure, а затем настроить приложение, чтобы отправить большой двоичный объект в учетную запись хранения Azure.


## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

См. дополнительные сведения о других [начальных приложениях Spring Boot для Microsoft Azure](spring-boot-starters-for-azure.md).

См. дополнительные сведения о других API-интерфейсах службы хранилища Azure, которые можно вызывать из приложений Spring Boot:
* [Использование хранилища BLOB-объектов Azure из Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Использование хранилища очередей Azure из Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Использование хранилища таблиц Azure из Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Использование хранилища файлов Azure из Java](/azure/storage/files/storage-java-how-to-use-file-storage)

<!-- IMG List -->

[IMG01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-01.png
[IMG02]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-02.png

[SI01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-01.png
