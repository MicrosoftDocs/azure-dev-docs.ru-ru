---
title: Как использовать Spring Boot Starter со службой хранилища Azure
description: Сведения о настройке приложения Spring Initializr с помощью начального приложения службы хранилища Azure.
services: storage
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage
ms.openlocfilehash: c4cc70cb97ec5e2199b41d768dbfd6313b19edcb
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68282975"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-storage"></a>Как использовать Spring Boot Starter со службой хранилища Azure

## <a name="overview"></a>Обзор

В статье описано, как создать пользовательское приложение с помощью **Spring Initializr** и добавить к нему начальное приложение службы хранилища Azure, а затем отправить большой двоичный объект в учетную запись хранения Azure с помощью этого приложения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться для получения [бесплатной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Интерфейс командной строки Azure (CLI)](http://docs.microsoft.com/cli/azure/overview).
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 и выше.

> [!IMPORTANT]
>
> Для выполнения операций, описанных в этой статье, требуется Spring Boot 2.0 или более поздней версии.
>

## <a name="create-an-azure-storage-account-and-blob-container-for-your-application"></a>Создание учетной записи хранения Azure и контейнера BLOB-объектов для приложения

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните **+Создать ресурс**, **Служба хранилища**, **Учетная запись хранения**.

   ![Создание учетной записи хранения Azure][IMG01]

1. На странице **Создание пространства имен** введите такую информацию:

   * Уникальное **имя**, которое станет частью URI для учетной записи хранения. Например, если вы зададите **wingtiptoysstorage** в качестве **имени**, URI примет вид *wingtiptoysstorage.core.windows.net*.
   * Выберите **Хранилище BLOB-объектов** в поле **Тип учетной записи**.
   * Укажите **расположение** для учетной записи хранения.
   * Выберите **подписку**, которую нужно использовать для учетной записи хранения.
   * Укажите, следует ли создать новую **группу ресурсов** для учетной записи хранения или использовать существующую.

   ![Создание учетной записи хранения Azure][IMG02]

1. Указав эти параметры, щелкните **Создать**, чтобы создать учетную запись хранения.

1. После создания учетной записи хранения на портале Azure щелкните **Большие двоичные объекты** и **+Контейнер**.

   ![Создание контейнера больших двоичных объектов][IMG03]

1. Введите **имя** для контейнера BLOB-объектов и щелкните **ОК**.

   ![Настройка параметров контейнера BLOB-объектов][IMG04]

1. Созданный контейнер BLOB-объектов отобразится в соответствующем списке на портале Azure.

   ![Список контейнеров BLOB-объектов][IMG05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Задайте такие параметры:

   * Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.
   * Выберите версию **Spring Boot** не ниже версии 2.0.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Добавьте зависимость **Web** (Веб).

      ![Основные параметры Spring Initializr][SI01]

   > [!NOTE]
   >
   > Spring Initializr использует имена **Group** (Группы) и **Artifact** (Артефакта) для создания имени пакета, как например *com.wingtiptoys.storage*.
   >

1. Указав эти параметры, щелкните **Generate Project** (Создать проект).

1. При появлении запроса скачайте проект на локальный компьютер.

   ![Скачивание проекта Spring][SI02]

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать.

## <a name="configure-your-spring-boot-app-to-use-the-azure-storage-starter"></a>Настройка приложения Spring Boot для использования начального приложения службы хранилища Azure

1. Найдите файл *pom.xml* в корневой папке приложения, например так:

   `C:\SpringBoot\storage\pom.xml`

   -или-

   `/users/example/home/storage/pom.xml`

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud для службы хранилища Azure в список `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-azure-starter-storage</artifactId>
      <version>1.0.0.M2</version>
   </dependency>
   ```

   ![Редактирование файла pom.xml][SI03]

1. Сохраните и закройте файл *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Создание файла учетных данных Azure

1. Откройте окно командной строки.

1. Перейдите к каталогу *resources* приложения Spring Boot, например так:

   ```shell
   cd C:\SpringBoot\storage\src\main\resources
   ```

   -или-

   ```shell
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

1. Найдите файл *application.properties* в каталоге *resources* приложения, например так:

   `C:\SpringBoot\storage\src\main\resources\application.properties`

   -или-

   `/users/example/home/storage/src/main/resources/application.properties`

2. Откройте файл *application.properties* в текстовом редакторе, добавьте указанные ниже строки в файл и замените примеры значений соответствующими параметрами учетной записи хранения:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.storage.account=wingtiptoysstorage
   ```
   Описание

   |                   Поле                   |                                            ОПИСАНИЕ                                            |
   |-------------------------------------------|---------------------------------------------------------------------------------------------------|
   | `spring.cloud.azure.credential-file-path` |            Определяет файл учетных данных Azure, который был создан ранее в этом примере.             |
   |    `spring.cloud.azure.resource-group`    |           Определяет группу ресурсов Azure, которая содержит учетную запись хранения Azure.            |
   |        `spring.cloud.azure.region`        | Определяет географический регион, указанный при создании учетной записи хранения Azure. |
   |   `spring.cloud.azure.storage.account`    |            Определяет учетную запись хранения Azure, которая была создана ранее в этом примере.             |


3. Сохраните и закройте файл *application.properties*.

## <a name="add-sample-code-to-implement-basic-azure-storage-functionality"></a>Добавление примера кода для реализации базовых функций службы хранилища Azure

В этом разделе вы создадите классы Java, требуемые для хранения большого двоичного объекта в учетной записи хранения Azure.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\StorageApplication.java`

   -или-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/StorageApplication.java`

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки:

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

### <a name="add-a-web-controller-class"></a>Добавление класса веб-контроллера

1. Создайте файл Java с именем *WebController.java* в каталоге пакета приложения, например так:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\WebController.java`

   -или-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/WebController.java`

1. Откройте файл Java веб-контроллера в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.WritableResource;
   import org.springframework.util.StreamUtils;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;

   import java.io.IOException;
   import java.io.OutputStream;
   import java.nio.charset.Charset;

   @RestController
   public class WebController {

      @Value("blob://test/myfile.txt")
      private Resource blobFile;

      @GetMapping(value = "/")
      public String readBlobFile() throws IOException {
         return StreamUtils.copyToString(
            this.blobFile.getInputStream(),
            Charset.defaultCharset()) + "\n";
      }

      @PostMapping(value = "/")
      public String writeBlobFile(@RequestBody String data) throws IOException {
         try (OutputStream os = ((WritableResource) this.blobFile).getOutputStream()) {
            os.write(data.getBytes());
         }
         return "File was updated.\n";
      }
   }
   ```

   Синтаксис `@Value("blob://[container]/[blob]")` определяет имена контейнера и большого двоичного объекта для хранения данных.

1. Сохраните и закройте файл Java веб-контроллера.

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*, например:

   `cd C:\SpringBoot\storage`

   -или-

   `cd /users/example/home/storage`

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. После запуска приложения можно использовать средство *curl*, чтобы протестировать приложение, например:

   a. Отправьте запрос POST, чтобы обновить содержимое файла:

      ```shell
      curl -X POST -H "Content-Type: text/plain" -d "Hello World" http://localhost:8080/
      ```

      Вы должны получить ответ, подтверждающий обновление файла.

   b. Отправьте запрос GET, чтобы проверить содержимое файла:

      ```shell
      curl -X GET http://localhost:8080/
      ```

     Должен отобразиться отправленный вами текст "Hello World".

## <a name="summary"></a>Сводка

В этом руководстве описано, как создать приложение Java с помощью **[Spring Initializr]** и добавить к нему начальное приложение службы хранилища Azure, а затем настроить приложение, чтобы отправить большой двоичный объект в учетную запись хранения Azure.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

См. дополнительные сведения о других [начальных приложениях Spring Boot для Microsoft Azure](spring-boot-starters-for-azure.md).

См. дополнительные сведения о других API-интерфейсах службы хранилища Azure, которые можно вызывать из приложений Spring Boot:
* [Использование хранилища BLOB-объектов Azure из Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Использование хранилища очередей Azure из Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Использование хранилища таблиц Azure из Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Использование хранилища файлов Azure из Java](/azure/storage/files/storage-java-how-to-use-file-storage)

<!-- IMG List -->

[IMG01]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-01.png
[IMG02]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-02.png
[IMG03]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-03.png
[IMG04]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-04.png
[IMG05]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-05.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-01.png
[SI02]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-02.png
[SI03]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-03.png
