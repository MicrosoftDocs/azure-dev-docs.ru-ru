---
title: Использование Spring Cloud Azure Stream Binder для служебной шины Azure
description: В этой статье показано, как использовать приложение Spring Cloud Azure Stream Binder для отправки сообщений в служебную шину Azure и получения сообщений из нее.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 02/04/2021
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 4223927cd99e652e8fb06dd8250c39b191f36a94
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822707"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Использование Spring Cloud Azure Stream Binder для служебной шины Azure

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

В этой статье показано, как использовать Spring Cloud Stream Binder для отправки сообщений в служебную шину и получения сообщений из нее `queues` и `topics`.

Azure предоставляет платформу асинхронного обмена сообщениями, именуемую [служебной шиной Azure](/azure/service-bus-messaging/service-bus-messaging-overview) ("служебная шина"), основанную на стандарте [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) (Расширенный протокол управления очередью сообщений "AMQP 1.0"). Служебную шину можно использовать в пределах поддерживаемых платформ Azure.

## <a name="prerequisites"></a>предварительные требования

Ниже перечислены предварительные требования.

1. Подписка Azure. Если у вас ее еще нет, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) или зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/).

1. Поддерживаемый пакет средств разработки Java (JDK) версии 8 или более поздней. Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.

1. [Apache Maven](http://maven.apache.org/) версии 3.2 или более поздней.

1. Если у вас уже есть настроенная очередь или раздел служебной шины, убедитесь, что пространство имен служебной шины соответствует следующим требованиям:

    1. Разрешен доступ из всех сетей
    1. Используется ценовая категория "Стандартный" (или выше)
    1. Имеется политика доступа с доступом на чтение и запись для вашей очереди и раздела

1. Если у вас нет настроенной очереди или раздела служебной шины, используйте портал Azure, чтобы [создать очередь служебной шины](/azure/service-bus-messaging/service-bus-quickstart-portal) или [создать раздел служебной шины](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Убедитесь, что пространство имен соответствует требованиям, указанным на предыдущем шаге. Кроме того, запишите строку подключения в пространстве имен так, как это требуется для тестового приложения этого руководства.

1. Если у вас нет приложения Spring Boot, **создайте проект** Maven [ с использованием Spring Initializr](https://start.spring.io/). Обязательно выберите **Проект Maven**, а затем в разделе **Зависимости** добавьте зависимость **Интернет**, для **Spring Boot** выберите 2.3.8, для версии Java выберите **8**.


## <a name="use-the-spring-cloud-stream-binder-starter"></a>Используйте пускатель Spring Cloud Stream Binder

1. Найдите файл *pom.xml* в родительском каталоге вашего приложения; например:

    `C:\SpringBoot\servicebus\pom.xml`

    -или-

    `/users/example/home/servicebus/pom.xml`

1. Затем откройте файл *pom.xml* в текстовом редакторе.

1. Добавьте следующий блок кода в элемент **&lt;Зависимости>** , в зависимости от того, используется очередь или раздел служебной шины:

    **очередь служебной шины Azure**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-queue</artifactId>
        <version>2.1.0</version>
    </dependency>
    ```

    **Раздел служебной шины**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-topic</artifactId>
        <version>2.1.0</version>
    </dependency>
    ```


1. Сохраните и закройте файл *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Настройка приложения для служебной шины

Настроить приложение можно на основе строки подключения или файла учетных данных. В этом руководстве используется строка подключения. Дополнительные сведения об использовании файлов учетных данных см. в разделе [Spring Cloud Azure Stream Binder for Service Bus queue Code Sample](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-cloud-sample-servicebus-queue-binder
) (Spring Cloud Azure Stream Binder для примера кода очереди служебной шины) и в разделе [Spring Cloud Azure Stream Binder for Service Bus topic Code Sample](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-cloud-stream-binder-servicebus-topic) (Spring Cloud Azure Stream Binder для примера кода раздела служебной шины).

1. Найдите файл *application.properties* в каталоге *resources*, например:

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   -или-

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. Откройте файл *application.properties* в текстовом редакторе.

1. Добавьте соответствующий код в конец файла *application.properties* в зависимости от того используется очередь или раздел служебной шины. Используйте [Таблицу описания полей](#fd), чтобы заменить образцы значений соответствующими свойствами служебной шины.

    **Очередь служебной шины**

    ```yaml
    spring:
      cloud:
        azure:
          servicebus:
            connection-string: <ServiceBusNamespaceConnectionString>
        stream:
          bindings:
            consume-in-0:
              destination: examplequeue
            supply-out-0:
              destination: examplequeue
          servicebus:
            queue:
              bindings:
                consume-in-0:
                  consumer:
                    checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            fixed-delay: 1000
            initial-delay: 0
    ```

    **Раздел служебной шины**

    ```yaml
    spring:
      cloud:
        azure:
          servicebus:
            connection-string: <ServiceBusNamespaceConnectionString>
        stream:
          bindings:
            consume-in-0:
              destination: exampletopic
              group: examplesubscription
            supply-out-0:
              destination: exampletopic
          servicebus:
            topic:
              bindings:
                consume-in-0:
                  consumer:
                    checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            fixed-delay: 1000
            initial-delay: 0
    ```

    **<a name="fd">Описания полей</a>**

    |                                        Поле                                   |                                                                                   Описание                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.function.definition`                |                                        Укажите, какой функциональный bean-компонент нужно привязать к внешним назначениям, предоставляемым привязками.                                   |
    |               `spring.cloud.azure.poller.fixed-delay`                |                                        Укажите фиксированную задержку для модуля опроса по умолчанию в миллисекундах (по умолчанию: 1000).                                   |
    |               `spring.cloud.azure.poller.initial-delay`                |                                       Укажите начальную задержку для периодических триггеров (по умолчанию: 0).                                   |
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Укажите строку подключения, полученную из пространства имен служебной шины на портале Azure.                                   |
    |               `spring.cloud.stream.bindings.consume-in-0.destination`                 |                            Укажите очередь служебной шины или раздел служебной шины, использованные в этом руководстве.                         |
    |                  `spring.cloud.stream.bindings.consume-in-0.group`                    |                                            Если вы использовали раздел служебной шины, укажите подписку на раздел.                                |
    |               `spring.cloud.stream.bindings.supply-out-0.destination`                |                               Укажите то же значение, которое использовалось для назначения ввода.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Укажите `MANUAL`.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Задайте имя `MANUAL`.                                                   |

1. Сохраните и закройте файл *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Реализация основных функций служебной шины

В этом разделе вы создадите классы Java, необходимые для отправки сообщений в служебную шину.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

    `C:\SpringBoot\servicebus\src\main\java\com\example\ServiceBusBinderApplication.java`

   -или-

   `/users/example/home/servicebus/src/main/java/com/example/ServiceBusBinderApplication.java`

1. Откройте главный файл приложения Java в текстовом редакторе.

1. Добавьте в него указанный ниже код.

    ```java
   package com.example;
   
   import com.azure.spring.integration.core.api.Checkpointer;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.context.annotation.Bean;
   import org.springframework.messaging.Message;
   
   import java.util.function.Consumer;
   
   import static com.azure.spring.integration.core.AzureHeaders.CHECKPOINTER;
   
   @SpringBootApplication
   public class ServiceBusBinderApplication {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceBusBinderApplication.class);
   
       public static void main(String[] args) {
           SpringApplication.run(ServiceBusBinderApplication.class, args);
       }
   
       @Bean
       public Consumer<Message<String>> consume() {
           return message -> {
               Checkpointer checkpointer = (Checkpointer) message.getHeaders().get(CHECKPOINTER);
               LOGGER.info("New message received: '{}'", message);
               checkpointer.success().handle((r, ex) -> {
                   if (ex == null) {
                       LOGGER.info("Message '{}' successfully checkpointed", message);
                   }
                   return null;
               });
           };
       }
   }
    ```

1. Сохраните файл и закройте его.

### <a name="create-a-new-producer-configuration-class"></a>Создание класса конфигурации производителя

1. С помощью текстового редактора создайте файл Java с именем *ServiceProducerConfiguration.java* в каталоге пакета приложения.

1. Добавьте в новый файл указанный ниже код:

    ```java
   package com.example;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.messaging.Message;
   import reactor.core.publisher.EmitterProcessor;
   import reactor.core.publisher.Flux;
   
   import java.util.function.Supplier;
   
   @Configuration
   public class ServiceProducerConfiguration {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceProducerConfiguration.class);
   
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

1. Сохраните и закройте файл *ServiceProducerConfiguration.java*.

### <a name="create-a-new-controller-class"></a>Создание класса контроллера

1. С помощью текстового редактора создайте файл Java с именем *ServiceProducerController.java* в каталоге пакета приложения.

1. Добавьте в новый файл следующие строки кода:

    ```java
   package com.example;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.ResponseEntity;
   import org.springframework.messaging.Message;
   import org.springframework.messaging.support.MessageBuilder;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;
   import reactor.core.publisher.EmitterProcessor;
   
   @RestController
   public class ServiceProducerController {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceProducerController.class);
   
       @Autowired
       private EmitterProcessor<Message<String>> emitterProcessor;
   
       @PostMapping("/messages")
       public ResponseEntity<String> sendMessage(@RequestParam String message) {
           LOGGER.info("Going to add message {} to emitter", message);
           emitterProcessor.onNext(MessageBuilder.withPayload(message).build());
           return ResponseEntity.ok("Sent!");
       }
   
       @GetMapping("/")
       public String welcome() {
           return "welcome";
       }
   }
    ```

1. Сохраните и закройте файл *ServiceProducerController.java*.

## <a name="build-and-test-your-application"></a>Сборка и тестирование приложения

1. Откройте командную строку.

1. Измените каталог на расположение файла *pom.xml*, например:

    `cd C:\SpringBoot\servicebus`

    -или-

    `cd /users/example/home/servicebus`

2. Создайте приложение Spring Boot с помощью Maven и запустите его:

    ```shell
    mvn clean spring-boot:run
    ```

3. После запуска приложения вы можете использовать средство *curl*, чтобы протестировать приложение:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    В журнале приложения появится запись "Привет":

    ```shell
    New message received: 'hello'
    Message 'hello' successfully checkpointed
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Spring в Azure](/java/azure/spring-framework)