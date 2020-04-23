---
title: Использование Spring Cloud Azure Stream Binder для служебной шины Azure
description: В этой статье показано, как использовать приложение Spring Cloud Azure Stream Binder для отправки сообщений в служебную шину Azure и получения сообщений из нее.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 08/21/2019
ms.topic: article
ms.openlocfilehash: 033025b82a493cf701abad7a6a97802611b7e48d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81669070"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Использование Spring Cloud Azure Stream Binder для служебной шины Azure

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

Azure предоставляет платформу асинхронного обмена сообщениями, именуемую [служебной шиной Azure](/azure/service-bus-messaging/service-bus-messaging-overview) ("служебная шина"), основанную на стандарте [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) (Расширенный протокол управления очередью сообщений "AMQP 1.0"). Служебную шину можно использовать в пределах поддерживаемых платформ Azure.

В этой статье показано, как использовать Spring Cloud Stream Binder для отправки сообщений в служебную шину и получения сообщений из нее `queues` и `topics`.

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены предварительные требования.

1. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/free/).

1. Поддерживаемый пакет средств разработки Java (JDK) версии 8 или более поздней. Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.

1. [Apache Maven](http://maven.apache.org/) версии 3.2 или более поздней.

1. Если у вас уже есть настроенная очередь или раздел служебной шины, убедитесь, что пространство имен служебной шины соответствует следующим требованиям:

    1. Разрешен доступ из всех сетей
    1. Используется ценовая категория "Стандартный" (или выше)
    1. Имеется политика доступа с доступом на чтение и запись для вашей очереди и раздела

1. Если у вас нет настроенной очереди или раздела служебной шины, используйте портал Azure, чтобы [создать очередь служебной шины](/azure/service-bus-messaging/service-bus-quickstart-portal) или [создать раздел служебной шины](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Убедитесь, что пространство имен соответствует требованиям, указанным на предыдущем шаге. Кроме того, запишите строку подключения в пространстве имен так, как это требуется для тестового приложения этого руководства.

1. Если у вас нет приложения Spring Boot, [создайте проект **Maven** со Spring Initializr](https://start.spring.io/). Не забудьте выбрать **Проект Maven** и, в разделе **Зависимости**, добавить зависимость **Интернет**.

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
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-queue-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![Измените файл pom.xml для очереди служебной шины.](media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-queue.png)

    **Раздел служебной шины**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-topic-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![Измените файл pom.xml для раздела служебной шины.](media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-topic.png)

1. Сохраните и закройте файл *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Настройка приложения для служебной шины

Настроить приложение можно на основе строки подключения или файла учетных данных. В этом руководстве используется строка подключения. Дополнительные сведения об использовании файлов учетных данных см. в разделе [Spring Cloud Azure Stream Binder for Service Bus queue Code Sample](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-queue-binder-sample#credential-file-based-usage
) (Spring Cloud Azure Stream Binder для примера кода очереди служебной шины) и в разделе [Spring Cloud Azure Stream Binder for Service Bus topic Code Sample](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-topic-binder-sample#credential-file-based-usage) (Spring Cloud Azure Stream Binder для примера кода раздела служебной шины).

1. Найдите файл *application.properties* в каталоге *resources*, например:

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   -или-

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. Откройте файл *application.properties* в текстовом редакторе.

1. Добавьте соответствующий код в конец файла *application.properties* в зависимости от того используется очередь или раздел служебной шины. Используйте [Таблицу описания полей](#fd), чтобы заменить образцы значений соответствующими свойствами служебной шины.

    **очередь служебной шины Azure**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=examplequeue
    spring.cloud.stream.bindings.output.destination=examplequeue
    spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **Раздел служебной шины**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=exampletopic
    spring.cloud.stream.bindings.input.group=examplesubscription
    spring.cloud.stream.bindings.output.destination=exampletopic
    spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **<a name="fd">Описания полей</a>**

    |                                        Поле                                   |                                                                                   Описание                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Укажите строку подключения, полученную из пространства имен служебной шины на портале Azure.                                   |
    |               `spring.cloud.stream.bindings.input.destination`                 |                            Укажите очередь служебной шины или раздел служебной шины, использованные в этом руководстве.                         |
    |                  `spring.cloud.stream.bindings.input.group`                    |                                            Если вы использовали раздел служебной шины, укажите подписку на раздел.                                |
    |               `spring.cloud.stream.bindings.output.destination`                |                               Укажите то же значение, которое использовалось для назначения ввода.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode` |                                                       Укажите `MANUAL`.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode` |                                                       Укажите `MANUAL`.                                                   |

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

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusBinderApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusBinderApplication.class, args);
        }
    }
    ```

1. Сохраните файл и закройте его.

### <a name="create-a-new-class-for-the-source-connector"></a>Создание класса для соединителя источника

1. С помощью текстового редактора создайте файл Java с именем *StreamBinderSource.java* в каталоге пакета приложения.

1. Добавьте в новый файл указанный ниже код:

    ```java
    package com.example;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.messaging.Source;
    import org.springframework.messaging.support.GenericMessage;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @EnableBinding(Source.class)
    @RestController
    public class StreamBinderSource {

        @Autowired
        private Source source;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            this.source.output().send(new GenericMessage<>(message));
            return message;
        }
    }
    ```

1. Сохраните и закройте файл *StreamBinderSources.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Создание класса для соединителя приемника

1. С помощью текстового редактора создайте файл Java с именем *StreamBinderSink.java* в каталоге пакета приложения.

1. Добавьте в новый файл следующие строки кода:

    ```java
    package com.example;

    import com.microsoft.azure.spring.integration.core.AzureHeaders;
    import com.microsoft.azure.spring.integration.core.api.Checkpointer;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.handler.annotation.Header;

    @EnableBinding(Sink.class)
    public class StreamBinderSink {

        @StreamListener(Sink.INPUT)
        public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
            System.out.println(String.format("New message received: '%s'", message));
            checkpointer.success().handle((r, ex) -> {
                if (ex == null) {
                    System.out.println(String.format("Message '%s' successfully checkpointed", message));
                }
                return null;
            });
        }
    }
    ```

1. Сохраните и закройте файл *StreamBinderSink.java*.

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