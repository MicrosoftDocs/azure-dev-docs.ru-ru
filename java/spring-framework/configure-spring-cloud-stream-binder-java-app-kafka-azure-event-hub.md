---
title: Использование начального приложения Spring Boot для Apache Kafka в Центрах событий Azure
description: Как настроить приложение, созданное с помощью Spring Boot Initializer, для использования Apache Kafka в Центрах событий Azure.
services: event-hubs
documentationcenter: java
ms.date: 12/19/2018
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: 6e6bee70c07538d06859254543886685308a0302
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812057"
---
# <a name="how-to-use-the-spring-boot-starter-for-apache-kafka-with-azure-event-hubs"></a>Использование начального приложения Spring Boot для Apache Kafka в Центрах событий Azure

В статье показано, как настроить приложение Spring Cloud Stream Binder на основе Java, созданное с помощью Spring Boot Initializr для использования [Apache Kafka] в Центрах событий Azure.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

> [!IMPORTANT]
>
> Для выполнения операций, описанных в этой статье, требуется Spring Boot 2.0 или более поздней версии.
>

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Создание концентратора событий на портале Azure

### <a name="create-an-azure-event-hub-namespace"></a>Создание пространства имен концентратора событий Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните **+Создать ресурс**, **Интернет вещей** и выполните поиск по запросу *Центры событий**.

1. Нажмите кнопку **Создать**.

   ![Создание пространства имен концентратора событий Azure][IMG01]

1. На странице **Создание пространства имен** введите такую информацию:

   * Уникальное **имя**, которое станет частью URI для пространства имен концентратора событий. Например, если вы зададите **wingtiptoys** в качестве **имени**, URI примет вид *wingtiptoys.servicebus.windows.net*.
   * Ценовая категория.
   * Отметьте **Включить Kafka** для пространства имен.
   * Выберите **подписку** для пространства имен.
   * Укажите, следует ли создать новую **группу ресурсов** для пространства имен или использовать существующую.
   * Укажите **расположение** для пространства имен Центров событий.
   * Можно также указать **единицы пропускной способности** для пространства имен.

   ![Создание пространства имен для концентратора событий Azure][IMG02]

1. Указав эти параметры, щелкните **Создать**, чтобы создать пространство имен.

### <a name="create-an-azure-event-hub-in-your-namespace"></a>Создание концентратора событий Azure в пространстве имен

Развернув пространство имен, можно создать в нем концентратор событий.

1. Перейдите к пространству имен, созданному на предыдущем шаге.

1. Щелкните **+Концентратор событий** в строке меню сверху.

1. Присвойте имя концентратору событий.

1. Нажмите кнопку **Создать**.

   ![Создание концентратора событий][IMG05]

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
   > Spring Initializr использует имена **Group** (Группы) и **Artifact** (Артефакта) для создания имени пакета, как например *com.wingtiptoys.kafka*.
   >

1. Указав эти параметры, щелкните **Generate Project** (Создать проект).

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать.

## <a name="configure-your-spring-boot-app-to-use-the-spring-cloud-kafka-stream-and-azure-event-hub-starters"></a>Настройка приложения Spring Boot для использования начальных приложений Spring Cloud Kafka Stream и концентратора событий Azure

1. Найдите файл *pom.xml* в корневой папке приложения, например так:

   `C:\SpringBoot\kafka\pom.xml`

   -или-

   `/users/example/home/kafka/pom.xml`

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud Kafka Stream и начальное приложение концентратора событий Azure в список `<dependencies>`:

   ```xml
   <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-stream-kafka</artifactId>
      <version>2.0.1.RELEASE</version>
   </dependency>
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-azure-starter-eventhub</artifactId>
      <version>1.0.0.M2</version>
   </dependency>
   ```

   ![Редактирование файла pom.xml][SI03]

1. Сохраните и закройте файл *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Создание файла учетных данных Azure

1. Откройте окно командной строки.

1. Перейдите к каталогу *resources* приложения Spring Boot, например так:

   ```shell
   cd C:\SpringBoot\eventhub\src\main\resources
   ```

   -или-

   ```shell
   cd /users/example/home/eventhub/src/main/resources
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

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Настройка приложения Spring Boot для использования концентратора событий Azure

1. Найдите файл *application.properties* в каталоге *resources* приложения, например так:

   `C:\SpringBoot\eventhub\src\main\resources\application.properties`

   -или-

   `/users/example/home/eventhub/src/main/resources/application.properties`

2. Откройте файл *application.properties* в текстовом редакторе, добавьте следующие строки и замените примеры значений соответствующими параметрами концентратора событий:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.eventhub.namespace=wingtiptoys

   spring.cloud.stream.bindings.input.destination=wingtiptoyshub
   spring.cloud.stream.bindings.input.group=$Default
   spring.cloud.stream.bindings.output.destination=wingtiptoyshub
   ```
   Описание

   |                       Поле                       |                                                                                   ОПИСАНИЕ                                                                                    |
   |---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `spring.cloud.azure.credential-file-path`     |                                                    Определяет файл учетных данных Azure, который был создан ранее в этом примере.                                                    |
   |        `spring.cloud.azure.resource-group`        |                                                      Определяет группу ресурсов Azure, которая содержит концентратор событий Azure.                                                      |
   |            `spring.cloud.azure.region`            |                                           Определяет географический регион, указанный при создании концентратора событий Azure.                                            |
   |      `spring.cloud.azure.eventhub.namespace`      |                                          Определяет уникальное имя, заданное при создании пространства имен концентратора событий Azure.                                           |
   | `spring.cloud.stream.bindings.input.destination`  |                            Определяет назначение входящих данных концентратора событий Azure, которым в этом примере является сам концентратор, созданный ранее.                            |
   |    `spring.cloud.stream.bindings.input.group `    | Определяет группу потребителей концентратора событий Azure, для которой можно установить значение $Default, чтобы использовать базовую группу потребителей, созданную вместе с концентратором событий Azure. |
   | `spring.cloud.stream.bindings.output.destination` |                               Определяет назначение исходящих данных для концентратора событий Azure, которое в этом примере совпадает с назначением входящих данных.                               |


3. Сохраните и закройте файл *application.properties*.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>Добавление примера кода для реализации базовых функций концентратора событий

В этом разделе вы создадите классы Java, требуемые для отправки событий в концентратор событий.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

   `C:\SpringBoot\kafka\src\main\java\com\wingtiptoys\kafka\KafkaApplication.java`

   -или-

   `/users/example/home/kafka/src/main/java/com/wingtiptoys/kafka/KafkaApplication.java`

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки:

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class KafkaApplication {
      public static void main(String[] args) {
         SpringApplication.run(KafkaApplication.class, args);
      }
   }
   ```

1. Сохраните и закройте файл основного приложения Java.


### <a name="create-a-new-class-for-the-source-connector"></a>Создание класса для соединителя источника

1. В каталоге пакета вашего приложения создайте файл Java с именем *KafkaSource.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.messaging.Source;
   import org.springframework.messaging.support.GenericMessage;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;

   @EnableBinding(Source.class)
   @RestController
   public class KafkaSource {
      @Autowired
      private Source source;

      @PostMapping("/messages")
      public String sendMessage(@RequestBody String message) {
         this.source.output().send(new GenericMessage<>(message));
         return message;
      }
   }
   ```

1. Сохраните и закройте файл *KafkaSource.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Создание класса для соединителя приемника

1. В каталоге пакета вашего приложения создайте файл Java с именем *KafkaSink.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.wingtiptoys.kafka;

   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;

   @EnableBinding(Sink.class)
   public class KafkaSink {
      private static final Logger LOGGER = LoggerFactory.getLogger(KafkaSink.class);

      @StreamListener(Sink.INPUT)
      public void handleMessage(String message) {
         LOGGER.info("New message received: " + message);
      }
   }
   ```

1. Сохраните и закройте файл *KafkaSink.java*.

## <a name="build-and-test-your-application"></a>Сборка и тестирование приложения

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*, например:

   `cd C:\SpringBoot\kafka`

   -или-

   `cd /users/example/home/kafka`

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. После запуска приложения можно использовать средство *curl*, чтобы протестировать приложение, например:

   ```shell
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   В журналах приложения должна появиться запись "hello". Например:

   ```shell
   [http-nio-8080-exec-2] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka version : 1.0.2
   [http-nio-8080-exec-2] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka commitId : 2a121f7b1d402825
   [wingtiptoyshub.container-0-C-1] INFO com.wingtiptoys.kafka.KafkaSink - New message received: hello
   ```


> [!NOTE]
> 
> Для тестирования можно изменить файл *KafkaSource.java*, добавив в него простую HTML-форму, как в следующем примере:
> 
> ```java
> package com.wingtiptoys.kafka;
>    
> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.cloud.stream.annotation.EnableBinding;
> import org.springframework.cloud.stream.messaging.Source;
> import org.springframework.messaging.support.GenericMessage;
> import org.springframework.web.bind.annotation.GetMapping;
> import org.springframework.web.bind.annotation.PostMapping;
> import org.springframework.web.bind.annotation.RequestBody;
> import org.springframework.web.bind.annotation.RequestParam;
> import org.springframework.web.bind.annotation.RestController;
> 
> @EnableBinding(Source.class)
> @RestController
> public class KafkaSource {
>   @Autowired
>   private Source source;
> 
>   @GetMapping("/")
>   public String sendForm() {
>     return "<html><body>" +
>       "<form action=\"/messages\" method=\"post\">" +
>       "<input type=\"text\" name=\"text\">" +
>       "<input type=\"submit\">" +
>       "</form></body><html>";
>     }
> 
>   @PostMapping("/messages")
>   public String sendMessage(@RequestBody String message) {
>     this.source.output().send(new GenericMessage<>(message));
>     return message;
>   }
> }
> ```
> 
> Это позволит использовать веб-браузер для тестирования приложения:
> 
> ![Тестирование приложения с помощью веб-браузера][TB01]
> 
> После отправки данных формы в приложении отобразятся результаты:
> 
> ![Ответ приложения в веб-браузере][TB02]
> 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о поддержке в Azure Apache Kafka и Stream Binder для концентратора событий см. в статьях:

* [Что такое Центры событий Azure?](/azure/event-hubs/event-hubs-about)

* [Центры событий Azure для Apache Kafka](/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)

* [Создание пространства имен службы "Центры событий" и концентратора событий с помощью портала Azure](/azure/event-hubs/event-hubs-create)

* [Создание Центров событий с поддержкой Apache Kafka](/azure/event-hubs/event-hubs-create-kafka-enabled)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Apache Kafka]: http://kafka.apache.org
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-01.png
[IMG02]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-02.png
[IMG03]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-03.png
[IMG04]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-04.png
[IMG05]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-05.png
[IMG06]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-06.png
[IMG07]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-07.png
[IMG08]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-08.png

[SI01]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-01.png
[SI02]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-02.png
[SI03]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-03.png

[TB01]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-01.png
[TB02]: ./media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-02.png
