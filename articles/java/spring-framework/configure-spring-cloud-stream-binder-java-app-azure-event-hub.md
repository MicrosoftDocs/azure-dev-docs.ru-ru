---
title: Создание приложения Spring Cloud Stream Binder с помощью Центров событий Azure
description: Настройка приложения Spring Cloud Stream Binder на основе Java, созданного с помощью Spring Boot Initializr и Центров событий Azure.
services: event-hubs
documentationcenter: java
ms.date: 02/08/2021
ms.service: event-hubs
ms.tgt_pltfrm: na
ms.topic: article
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d0c87ce32caddc0100b91abd800a18179ba4101e
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822725"
---
# <a name="how-to-create-a-spring-cloud-stream-binder-application-with-azure-event-hubs"></a>Создание приложения Spring Cloud Stream Binder с помощью Центров событий Azure

В статье показано, как настроить приложение Spring Cloud Stream Binder на основе Java, созданное с помощью Spring Boot Initializr и Центров событий Azure.

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

> [!IMPORTANT]
> Для выполнения операций, описанных в этой статье, требуется Spring Boot 2.2 или 2.3.

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Создание концентратора событий на портале Azure

Следующая процедура создает концентратор событий Azure.

### <a name="create-an-azure-event-hub-namespace"></a>Создание пространства имен концентратора событий Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Выберите **+ Создать ресурс** и выполните поиск по фразе *Центры событий*.

1. Выберите **Создать**.

   >[!div class="mx-imgBorder"]
   >![Создание пространства имен концентратора событий Azure][IMG01]

1. На странице **Создание пространства имен** введите такую информацию:

   * Выберите **подписку** для пространства имен.
   * Укажите, следует ли создать новую **группу ресурсов** для пространства имен или использовать существующую.
   * Уникальное **имя пространства имен**, которое станет частью URI для пространства имен концентратора событий. Например, если в поле **Имя пространства имен** вы укажете *wingtiptoys-space*, URI примет вид `wingtiptoys-space.servicebus.windows.net`.
   * Укажите **расположение** для пространства имен Центров событий.
   * Ценовая категория.
   * Можно также указать **единицы пропускной способности** для пространства имен.
   
   >[!div class="mx-imgBorder"]
   >![Создание пространства имен для концентратора событий Azure][IMG02]

1. Указав приведенные выше параметры, выберите **Просмотр и создание**, проверьте спецификации и щелкните **Создать**, чтобы создать пространство имен.

## <a name="create-an-azure-event-hub-in-your-namespace"></a>Создание концентратора событий Azure в пространстве имен

После развертывания пространства имен выберите **Перейти к ресурсу**, чтобы открыть страницу **Пространство имен Центров событий**, где можно создать концентратор событий в пространстве имен.

1. Перейдите к пространству имен, созданному по инструкциям из предыдущего раздела.

1. Выберите **+ Центры событий** в строке меню вверху.

1. Присвойте имя концентратору событий.

1. Выберите **Создать**.

   >[!div class="mx-imgBorder"]
   >![Создание концентратора событий][IMG05]

### <a name="create-an-azure-storage-account-for-your-event-hub-checkpoints"></a>Создание учетной записи хранения для контрольных точек концентратора событий

Следующая процедура создает учетную запись хранения для контрольных точек концентратора событий.

1. Перейдите на портал Azure по адресу <https://portal.azure.com/>.

1. Выберите **+ Создать ресурс**, **Хранилище** и **Учетная запись хранения**.

1. На странице **Создание учетной записи хранения** введите следующие сведения:

   * Выберите **подписку**, которую нужно использовать для учетной записи хранения.
   * Укажите, следует ли создать новую **группу ресурсов** для учетной записи хранения или использовать существующую.
   * Введите уникальное **имя** учетной записи хранения.
   * Укажите **расположение** для учетной записи хранения.

   >[!div class="mx-imgBorder"]
   >![Создание учетной записи хранения Azure][IMG08]

1. Указав эти параметры, выберите **Просмотр и создание**, чтобы создать учетную запись хранения.

1. Проверьте спецификации и щелкните **Создать**.  Развертывание займет несколько минут.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

Следующая процедура создает приложение Spring Boot.

1. Перейдите по адресу <https://start.spring.io/>.

1. Задайте такие параметры:

   * Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.
   * Укажите версию **Spring Boot** **2.3**.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Выберите версию Java **8**.
   * Добавьте зависимость *Web* (Веб).

   >[!div class="mx-imgBorder"]
   >![Основные параметры Spring Initializr][SI01]

   > [!NOTE]
   > Spring Initializr использует имена в полях **Group** (Группа) и **Artifact** (Артефакт) для создания имени пакета, например *com.contoso.eventhubs.sample*.

1. Указав перечисленные выше параметры, щелкните **GENERATE** (Создать).

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать.

## <a name="configure-your-spring-boot-app-to-use-the-azure-event-hub-starter"></a>Настройка приложения Spring Boot для использования начального приложения концентратора событий Azure

1. Найдите файл *pom.xml* в корневой папке приложения, например так:

   *C:\SpringBoot\eventhubs-sample\pom.xml*

   -или-

   */users/example/home/eventhubs-sample/pom.xml*

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud Stream Binder для концентратора событий Azure в список `<dependencies>`:

   ```xml
   <dependency>
     <groupId>com.azure.spring</groupId>
     <artifactId>azure-spring-cloud-stream-binder-eventhubs</artifactId>
     <version>2.1.0</version>
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

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Настройка приложения Spring Boot для использования концентратора событий Azure

1. Найдите файл *application.yaml* в каталоге *resources* приложения, например:

   *C:\SpringBoot\eventhubs-sample\src\main\resources\application.yaml*

   -или-

   */users/example/home/eventhubs-sample/src/main/resources/application.yaml*

2. Откройте файл *application.yaml* в текстовом редакторе, добавьте следующие строки и замените примеры значений соответствующими свойствами центра событий:

   ```yaml
    spring:
      cloud:
        azure:
          eventhub:
            connection-string: [eventhub-namespace-connection-string]
            checkpoint-storage-account: wingtiptoysstorage
            checkpoint-access-key: [checkpoint-access-key]
            checkpoint-container: wingtiptoyscontainer
            
        stream:
          bindings:
            consume-in-0:
              destination: wingtiptoyshub
              group: $Default
            supply-out-0:
              destination: wingtiptoyshub
   
          eventhub:
            bindings:
              consume-in-0:
                consumer:
                  checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            initial-delay: 0
            fixed-delay: 1000
   ```

   Где:

   |                          Поле                           |                                                                                   Описание                                                                                    |
   |----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |               `spring.cloud.azure.eventhub.connection-string`                |                                        Укажите строку подключения, полученную из пространства имен Центра событий на портале Azure.                                   |
   |               `spring.cloud.azure.function.definition`                |                                        Укажите, какой функциональный bean-компонент нужно привязать к внешним назначениям, предоставляемым привязками.                                   |
   |               `spring.cloud.azure.poller.fixed-delay`                |                                        Укажите фиксированную задержку для модуля опроса по умолчанию в миллисекундах (по умолчанию: 1000).                                   |
   |               `spring.cloud.azure.poller.initial-delay`                |                                       Укажите начальную задержку для периодических триггеров (по умолчанию: 0).                                   |
   |               `spring.cloud.stream.bindings.consume-in-0.destination`                 |                            Укажите Центр событий, который использовался в этом учебнике.                         |
   |               `spring.cloud.stream.bindings.consume-in-0.group`                    |                               Укажите группы потребителей в экземпляре Центра событий.                                |
   |               `spring.cloud.stream.bindings.supply-out-0.destination`                |                             Укажите тот же Центр событий, который использовался в этом учебнике.                        |
   | `spring.cloud.stream.eventhub.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Задайте имя `MANUAL`.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-access-key` |                                                      Укажите ключ доступа к учетной записи хранения.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-container` |                                                       Укажите контейнер учетной записи хранения.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-storage-account` |                                                 Укажите учетную запись хранения, созданную при работе с этим учебником.                                               |

3. Сохраните и закройте файл *application.yaml*.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>Добавление примера кода для реализации базовых функций концентратора событий

В этом разделе вы создадите классы Java, требуемые для отправки событий в концентратор событий.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

   *C:\SpringBoot\eventhubs-sample\src\main\java\com\contoso\eventhubs\sample\EventhubSampleApplication.java*

   -или-

   */users/example/home/eventhubs-sample/src/main/java/com/contoso/eventhubs/sample/EventhubSampleApplication.java*

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки:

   ```java
    package com.contoso.eventhubs.sample;
    
    import com.azure.spring.integration.core.api.reactor.Checkpointer;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;
    import org.springframework.messaging.Message;
    
    import java.util.function.Consumer;
    
    import static com.azure.spring.integration.core.AzureHeaders.CHECKPOINTER;
    
    @SpringBootApplication
    public class EventhubSampleApplication {
    
        public static final Logger LOGGER = LoggerFactory.getLogger(EventhubSampleApplication.class);
    
        public static void main(String[] args) {
            SpringApplication.run(EventhubSampleApplication.class, args);
        }
    
        @Bean
        public Consumer<Message<String>> consume() {
            return message -> {
                Checkpointer checkpointer = (Checkpointer) message.getHeaders().get(CHECKPOINTER);
                LOGGER.info("New message received: '{}'", message);
                checkpointer.success()
                            .doOnSuccess(success -> LOGGER.info("Message '{}' successfully checkpointed", message))
                            .doOnError(error -> LOGGER.error("Exception: {}", error.getMessage()))
                            .subscribe();
            };
        }
    
    }
   ```

1. Сохраните и закройте файл основного приложения Java.

### <a name="create-a-new-configuration-class"></a>Создание класса конфигурации

1. В каталоге пакета приложения создайте файл Java с именем *EventProducerConfiguration.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

    ```java
    package com.contoso.eventhubs.sample;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.messaging.Message;
    import reactor.core.publisher.EmitterProcessor;
    import reactor.core.publisher.Flux;
    
    import java.util.function.Supplier;
    
    @Configuration
    public class EventProducerConfiguration {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(EventProducerConfiguration.class);
    
        @Bean
        public EmitterProcessor<Message<String>> emitter() {
            return EmitterProcessor.create();
        }
    
        @Bean
        public Supplier<Flux<Message<String>>> supply(EmitterProcessor<Message<String>> emitter) {
            return () -> Flux.from(emitter)
                             .doOnNext(m -> LOGGER.info("Manually sending message {}", m))
                             .doOnError(t -> LOGGER.error("Error encountered", t));
        }
    }
    ```
1. Сохраните и закройте файл *EventProducerConfiguration.java*.

### <a name="create-a-new-controller-class"></a>Создание класса контроллера

1. В каталоге пакета приложения создайте файл Java с именем *EventProducerController.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.contoso.eventhubs.sample;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.ResponseEntity;
   import org.springframework.messaging.Message;
   import org.springframework.messaging.support.MessageBuilder;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;
   import reactor.core.publisher.EmitterProcessor;
   
   @RestController
   public class EventProducerController {
   
       public static final Logger LOGGER = LoggerFactory.getLogger(EventProducerController.class);
   
       @Autowired
       private EmitterProcessor<Message<String>> emitterProcessor;
   
       @PostMapping("/messages")
       public ResponseEntity<String> sendMessage(@RequestBody String message) {
           LOGGER.info("Going to add message {} to emitter", message);
           emitterProcessor.onNext(MessageBuilder.withPayload(message).build());
           return ResponseEntity.ok("Sent!");
       }
   }
   ```

1. Сохраните и закройте файл *EventProducerController.java*.

## <a name="build-and-test-your-application"></a>Сборка и тестирование приложения

Используйте следующую процедуру для создания и тестирования приложения.

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*, например:

   ```cmd
    cd C:\SpringBoot\eventhubs-sample
   ```
   -или-

   ```bash
   cd /users/example/home/eventhubs-sample
   ```

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```bash
   mvn clean package -Dmaven.test.skip=true
   mvn spring-boot:run
   ```

1. После запуска приложения можно использовать средство `curl`, чтобы протестировать приложение, например:

   ```bash
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   В журналах приложения должна появиться запись "hello". Пример:

   ```output
   2020-09-11 15:11:12.138  INFO 7616 --- [      elastic-4] c.contoso.eventhubs.sample.EventhubSampleApplication  : New message received: 'hello'
   2020-09-11 15:11:12.406  INFO 7616 --- [ctor-http-nio-1] c.contoso.eventhubs.sample.EventhubSampleApplication  : Message 'hello' successfully checkpointed
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о поддержке в Azure приложения Event Hub Stream Binder для концентратора событий см. в следующих статьях:

* [Что такое Центры событий Azure?](/azure/event-hubs/event-hubs-about)

* [Создание пространства имен службы "Центры событий" и концентратора событий с помощью портала Azure](/azure/event-hubs/event-hubs-create)

* [How to use the Spring Boot Starter for Apache Kafka with Azure Event Hubs](configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub.md) (Использование начального приложения Spring Boot для Apache Kafka в Центрах событий)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Azure для разработчиков Java]: ../index.yml
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-02.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-05.png
[IMG08]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-08.png
[SI01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-01.png
