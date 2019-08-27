---
title: Использование Spring Boot Starter для служебных шин Azure JMS
description: В этой статье показано, как использовать приложение Spring JMS Starter для отправки сообщений в служебную шину Azure и получения сообщений из нее.
author: seanli1988
manager: kyliel
ms.author: Sean.Li
ms.date: 08/21/2019
ms.devlang: java
ms.service: azure-java
ms.topic: article
ms.openlocfilehash: f41486c7063a6b0fa26ca4055d5f10e625676e8a
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892378"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Использование Spring Boot Starter для служебных шин Azure JMS

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

Azure предоставляет платформу асинхронного обмена сообщениями, именуемую [служебной шиной Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) ("служебная шина"), основанную на стандарте [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) (Расширенный протокол управления очередью сообщений "AMQP 1.0"). Служебную шину можно использовать в пределах поддерживаемых платформ Azure.

Spring Boot Starter для служебной шины Azure JMS обеспечивает интеграцию Spring со служебной шиной.

В этой статье показано, как использовать Spring Boot Starter для служебной шины Azure JMS для отправки сообщений в служебную шину Azure и получения сообщений из нее `queues` и `topics`.

> [!NOTE]
> В настоящее время в этой статье используется Starter версии `SNAPSHOT`.

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены предварительные требования.

1. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.comfree/).

1. Поддерживаемый пакет средств разработки Java (JDK) версии 8 или более поздней. Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.

1. [Apache Maven](http://maven.apache.org/) версии 3.2 или более поздней.

1. Если у вас уже есть настроенная очередь или раздел служебной шины, убедитесь, что пространство имен служебной шины соответствует следующим требованиям:

    1. Разрешен доступ из всех сетей
    1. Является Premium (или выше)
    1. Имеется политика доступа с доступом на чтение и запись для вашей очереди и раздела

1. Если у вас нет настроенной очереди или раздела служебной шины, используйте портал Azure, чтобы [создать очередь служебной шины](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) или [создать раздел служебной шины](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Убедитесь, что пространство имен соответствует требованиям, указанным на предыдущем шаге. Кроме того, запишите строку подключения в пространстве имен так, как это требуется для тестового приложения этого руководства.

1. Если у вас нет приложения Spring Boot, [создайте проект **Maven** с Spring Initializer](https://start.spring.io/). Не забудьте выбрать **Проект Maven** и, в разделе **Зависимости**, добавить зависимость **Интернет**.

## <a name="use-the-azure-service-bus-jms-starter"></a>Использование JMS starter служебной шины Azure

1. Найдите файл *pom.xml* в родительском каталоге вашего приложения; например:

    `C:\SpringBoot\servicebus\pom.xml`

    -или-

    `/users/example/home/servicebus/pom.xml`

1. Затем откройте файл *pom.xml* в текстовом редакторе.

1. Добавьте в список `<dependencies>` JMS Starter служебной шины Azure приложения Spring Boot:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms-spring-boot-starter</artifactId>
        <version>2.1.7-SNAPSHOT</version>
    </dependency>
    ```

    ![Добавьте раздел зависимостей в файл pom.xml.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-dependency-section.png)

1. Добавьте конфигурацию [репозиториев Maven](https://maven.apache.org/settings.html#Repositories) для использования версии моментального снимка:

    ```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>
    ```

    ![Добавить раздел репозиториев в файл pom.xml.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-repository-section.png)

1. Сохраните и закройте файл *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Настройка приложения для служебной шины 

В этом разделе вы узнаете, как настроить приложение для использования очереди или раздела служебной шины.

### <a name="use-a-service-bus-queue"></a>Использование очереди служебной шины

1. Найдите файл *application.properties* в каталоге *resources* приложения, например так:

    `C:\SpringBoot\servicebus\application.properties`

    -или-

    `/users/example/home/servicebus/application.properties`

1. Откройте файл *application.properties* в текстовом редакторе.

1. Добавьте следующий код в конец файла *application.properties*. Замените примеры значений соответствующими значениями для служебной шины:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Описания полей**

    | Поле                                     | ОПИСАНИЕ                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Укажите строку подключения, полученную из пространства имен служебной шины на портале Azure. |
    | `spring.jms.servicebus.idle-timeout`      | Укажите время ожидания перед переходом в режим простоя в миллисекундах. Рекомендуемое в этом руководстве значение — 1800000.   |

3. Сохраните и закройте файл *application.properties*.

### <a name="use-service-bus-topic"></a>Использование раздела служебной шины

1. Найдите файл *application.properties* в каталоге *resources* приложения, например так:

    `C:\SpringBoot\servicebus\application.properties`

    -или-

    `/users/example/home/servicebus/application.properties`

1. Откройте файл *application.properties* в текстовом редакторе.

1. Добавьте следующий код в конец файла *application.properties*. Замените примеры значений соответствующими значениями для служебной шины:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusTopicClientId>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Описания полей**

    | Поле                                     | ОПИСАНИЕ                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Укажите строку подключения, полученную из пространства имен служебной шины на портале Azure.   |
    | `spring.jms.servicebus.topic-client-id`   | Укажите идентификатор клиента JMS, если вы используете раздел служебной шины Azure с устойчивой подпиской. |
    | `spring.jms.servicebus.idle-timeout`      | Укажите время ожидания перед переходом в режим простоя в миллисекундах. Рекомендуемое в этом руководстве значение — 1800000.     |

1. Сохраните и закройте файл *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Реализация основных функций служебной шины

В этом разделе вы создадите необходимые классы Java для отправки сообщений в очередь или раздел служебной шины и получите сообщения из соответствующей подписки на очередь или раздел.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

    `C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java`

    -или-

    `/users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java`

1. Откройте главный файл приложения Java в текстовом редакторе.

1. Добавьте в него указанный ниже код.

   ```java
    package com.wingtiptoys.servicebus;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusJmsStarterApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusJmsStarterApplication.class, args);
        }
    }
    ```

1. Сохраните и закройте файл.

### <a name="define-a-test-java-class"></a>Определение тестового класса Java

1. С помощью текстового редактора создайте файл Java с именем *User.java* в каталоге пакета приложения.

1. Определите общий класс пользователя, который хранит и получает имя пользователя:

    ```java
    package com.wingtiptoys.servicebus;

    import java.io.Serializable;

    // Define a generic User class.
    public class User implements Serializable {

        private static final long serialVersionUID = -295422703255886286L;

        private String name;

        public User() {
        }

        public User(String name) {
            setName(name);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }
    ```

    `Serializable` реализуется для использования метода `send` в `JmsTemplate` на платформе Spring. В противном случае необходимо определить настроенный `MessageConverter` bean-компонент для сериализации содержимого в JSON в текстовом формате. Дополнительные сведения о `MessageConverter` см. в официальном [проекте Spring JMS starter](https://spring.io/guides/gs/messaging-jms/).

1. Сохраните и закройте файл *User.java*.

### <a name="create-a-new-class-for-the-message-send-controller"></a>Создание нового класса для контроллера отправки сообщений

1. С помощью текстового редактора создайте файл Java с именем *SendController.java* в каталоге пакета приложения

1. Добавьте в новый файл указанный ниже код:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jms.core.JmsTemplate;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SendController {

        private static final String DESTINATION_NAME = "<DestinationName>";

        private static final Logger logger = LoggerFactory.getLogger(SendController.class);

        @Autowired
        private JmsTemplate jmsTemplate;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            logger.info("Sending message");
            jmsTemplate.convertAndSend(DESTINATION_NAME, new User(message));
            return message;
        }
    }
    ```

    > [!NOTE]
    > Замените `<DestinationName>` именем своей очереди или именем раздела, настроенным в пространстве имен служебной шины.

1. Сохраните и закройте *SendController.java*.

### <a name="create-a-class-for-the-message-receive-controller"></a>Создание нового класса для контроллера получения сообщений

#### <a name="receive-messages-from-a-service-bus-queue"></a>Получение сообщений из очереди служебной шины

1. С помощью текстового редактора создайте файл Java с именем *QueueReceiveController.java* в каталоге пакета приложения

1. Добавьте в новый файл указанный ниже код:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class QueueReceiveController {

        private static final String QUEUE_NAME = "<ServiceBusQueueName>";

        private final Logger logger = LoggerFactory.getLogger(QueueReceiveController.class);

        @JmsListener(destination = QUEUE_NAME, containerFactory = "jmsListenerContainerFactory")
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

    > [!NOTE]
    > Замените `<ServiceBusQueueName>` именем своей очереди, настроенным в пространстве имен служебной шины.

1. Сохраните и закройте файл *QueueReceiveController.java*.

#### <a name="receive-messages-from-a-service-bus-subscription"></a>Получение сообщений из подписки служебной шины

1. С помощью текстового редактора создайте файл Java с именем *TopicReceiveController.java* в каталоге пакета приложения. 

1. Добавьте в новый файл указанный ниже код. Замените `<ServiceBusTopicName>` заполнитель именем своего раздела, настроенным в пространстве имен служебной шины. Замените `<ServiceBusSubscriptionName>` заполнитель именем своей подписки, настроенным в пространстве имен служебной шины.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class TopicReceiveController {

        private static final String TOPIC_NAME = "<ServiceBusTopicName>";

        private static final String SUBSCRIPTION_NAME = "<ServiceBusSubscriptionName>";

        private final Logger logger = LoggerFactory.getLogger(TopicReceiveController.class);

        @JmsListener(destination = TOPIC_NAME, containerFactory = "topicJmsListenerContainerFactory",
                subscription = SUBSCRIPTION_NAME)
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

1. Сохраните и закройте файл *TopicReceiveController.java*.

## <a name="build-and-test-your-application"></a>Сборка и тестирование приложения

1. Откройте командную строку и измените каталог на расположение файла *pom.xml*, например:

    `cd C:\SpringBoot\servicebus`

    -или-

    `cd cd /users/example/home/servicebus`

1. Создайте приложение Spring Boot с помощью Maven и запустите его:

    ```shell
    mvn clean spring-boot:run
    ```

3. После запуска приложения вы можете использовать средство *curl*, чтобы протестировать приложение:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    В журналах приложения должна появиться запись "Отправка сообщения" и "Привет":

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](http://ms.portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [How to use the Spring Boot Starter for Azure Service Bus JMS](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp) (Использование JMS API с служебной шиной и AMQP 1.0)