---
title: Асинхронное программирование в пакете SDK Azure для Java
description: Общие сведения о модели асинхронного программирования в пакете SDK Azure для Java
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 64a692b10175a7c08ff8a32e56a638209f239c19
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528713"
---
# <a name="asynchronous-programming-in-the-azure-sdk-for-java"></a>Асинхронное программирование в пакете SDK Azure для Java

В этой статье описывается модель асинхронного программирования в пакете SDK Azure для Java.

Изначально пакет SDK Azure содержал только асинхронные неблокирующие API для взаимодействия со службами Azure. Эти API позволяют использовать пакет SDK Azure для создания масштабируемых приложений, которые эффективно используют системные ресурсы. Однако [пакет SDK Azure для Java](https://github.com/Azure/azure-sdk-for-java#client-new-releases) также содержит синхронные клиенты, предназначенные для более широкой аудитории. Они также делают клиентские библиотеки доступными для пользователей, которые не знакомы с асинхронным программированием. (См. раздел [Доступность](https://azure.github.io/azure-sdk/general_introduction.html#approachable) руководства по разработке пакета SDK для Azure.) Таким образом, все клиентские библиотеки Java в пакете SDK Azure для Java предоставляют как асинхронные, так и синхронные клиенты. Однако для производственных систем рекомендуется использовать асинхронные клиенты. Это позволит максимально эффективно использовать системные ресурсы.

## <a name="reactive-streams"></a>Реактивные потоки

В разделе [Клиенты асинхронной службы](https://azure.github.io/azure-sdk/java_introduction.html#async-service-clients) статьи [Рекомендации по проектированию пакета Azure SDK для Java](https://azure.github.io/azure-sdk/java_introduction.html) указано, что вместо класса `CompletableFuture`, предоставляемого Java 8, наши асинхронные API используют реактивные типы потоков. Почему мы используем реактивные типы потоков вместо типов, которые изначально доступны в JDK?

В Java 8 появились такие функции, как [потоки](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html), [лямбда-выражения](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) и [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html). Эти функции предоставляют множество возможностей, но имеют некоторые ограничения.

`CompletableFuture` предоставляет неблокирующие возможности на основе обратных вызовов, а интерфейс `CompletionStage` позволяет легко создавать серию асинхронных операций. Лямбда-выражения делают эти API на основе принудительной передачи более удобными для чтения. Потоки предоставляют функциональные операции для управления коллекцией элементов данных. Однако потоки являются синхронными и не могут использоваться повторно. `CompletableFuture` позволяет выполнить один запрос, обеспечивает поддержку обратного вызова и ожидает _один_ ответ. Однако многие облачные службы нуждаются в возможности потоковой передачи данных (например, концентраторы событий).

Реактивные потоки могут помочь устранить эти ограничения путем потоковой передачи элементов из источника в базу данных подписчика. Когда подписчик запрашивает данные из источника, источник возвращает любое количество результатов. Их не нужно передавать сразу. Передача происходит в течение определенного периода времени, когда в источнике появляются данные для отправки.

В этой модели подписчик регистрирует обработчики событий для обработки данных при их поступлении. Эти взаимодействия на основе принудительной отправки уведомляют подписчика с помощью различных сигналов:

- Вызов `onSubscribe()` указывает, что начнется перемещение данных.
- Вызов `onError()` указывает на ошибку, которая также свидетельствует об окончании передачи данных.
- Вызов `onComplete()` указывает на успешное завершение передачи данных.

В отличие от потоков Java, реактивные потоки обрабатывают ошибки как события первого класса. Реактивные потоки имеют выделенный канал, чтобы источник мог передавать любые ошибки в базу данных подписчика. Кроме того, реактивные потоки позволяют подписчику согласовать скорость передачи данных для преобразования этих потоков в модель опрашивания и запрашивания.

Спецификация [реактивных потоков](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams) предоставляет стандарт, который будет использоваться для перемещения данных. На высоком уровне спецификация определяет указанные ниже четыре интерфейса, а также правила их реализации.

- **Издатель** является источником потока данных.
- **Подписчик** является потребителем потока данных.
- **Подписка** позволяет управлять состоянием обмена данными между издателем и подписчиком.
- **Обработчик** может быть как издателем, так и подписчиком.

Существуют некоторые хорошо известные библиотеки Java, которые предоставляют реализации этой спецификации, такие как [RxJava](https://github.com/ReactiveX/RxJava), [Akka Streams](https://doc.akka.io/docs/akka/current/stream/stream-introduction.html), [Vert.x](https://vertx.io/docs/#reactive) и [Project Reactor](https://projectreactor.io/docs/core/release/reference/).

В пакете SDK Azure для Java для предоставления асинхронных API-интерфейсов реализован Project Reactor. Основным фактором, повлиявшим на это решение, было обеспечение беспрепятственной интеграции с [Spring Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html), который также использует Project Reactor. Второй фактор, повлиявший на выбор Project Reactor вместо RxJava, заключался в том, что Project Reactor использует Java 8, а RxJava по-прежнему Java 7. Project Reactor также предлагает обширный набор составляемых операторов, которые позволяют писать декларативный код для создания конвейеров обработки данных. Еще одно преимущество Project Reactor заключается в том, что в нем есть адаптеры для преобразования типов Project Reactor в другие популярные типы реализации.

## <a name="comparing-apis-of-synchronous-and-asynchronous-operations"></a>Сравнение интерфейсов API для синхронных и асинхронных операций

Мы рассмотрели синхронные клиенты и параметры для асинхронных клиентов. В следующей таблице приведено краткое описание интерфейсов API, разработанных с использованием следующих параметров:

| Тип API                                           | Нет значения                   | Однозначный            | Несколько значений                         |
|----------------------------------------------------|----------------------------|-------------------------|-------------------------------|
| Стандартные синхронные API-интерфейсы Java                   | `void`                     | `T`                     | `Iterable<T>`                 |
| Стандартные асинхронные API-интерфейсы Java                  | `CompletableFuture<Void>`  | `CompletableFuture<T>`  | `CompletableFuture<List<T>>`   |
| Интерфейсы реактивных потоков                        | `Publisher<Void>`          | `Publisher<T>`          | `Publisher<T>`                 |
| Реализация реактивных потоков в Project Reactor | `Mono<Void>`               | `Mono<T>`               | `Flux<T>`                      |

Для полноты понимания стоит упомянуть, что в Java 9 появился класс [Flow](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Flow.html), который включает четыре интерфейса для реактивных потоков. Однако этот класс не содержит никакой реализации.

## <a name="use-async-apis-in-the-azure-sdk-for-java"></a>Использование асинхронных API-интерфейсов в пакете SDK Azure для Java

Спецификация реактивных потоков не различает типы издателей. В спецификации реактивных потоков издатели просто создают ноль или более элементов данных. Во многих случаях существует полезное различие между издателем, создающим максимум один элемент данных, и издателем, создающим ноль или более элементов. В облачных API это различие указывает, возвращает ли запрос однозначный ответ или коллекцию. Project Reactor предоставляет два типа, чтобы указать это различие — [Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html) и [Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html). API-интерфейс, возвращающий `Mono`, будет содержать ответ, который имеет не более одного значения, а API, возвращающий `Flux`, — ответ, который имеет ноль или более значений.

Предположим, что вы используете [ConfigurationAsyncClient](/java/api/com.azure.data.appconfiguration.configurationasyncclient) для получения конфигурации, хранящейся с помощью службы "Конфигурация приложений Azure". (Дополнительные сведения см. в статье [Что такое служба конфигурации приложений Azure?](/azure/azure-app-configuration/overview))

Если вы создаете `ConfigurationAsyncClient` и вызываете `getConfigurationSetting()` в клиенте, он возвращает `Mono`. Это означает, что ответ содержит одно значение. Однако при вызове одного метода не будет выполнено никаких действий. Клиент еще не выполнил запрос к службе "Конфигурация приложений Azure". На этом этапе `Mono<ConfigurationSetting>`, возвращаемый этим API, — это просто "сборка" конвейера обработки данных. Это означает, что необходимая настройка для использования данных завершена. Чтобы непосредственно запустить передачу данных (то есть выполнить запрос к службе и получить ответ), необходимо подписаться на возвращенный `Mono`. Поэтому при работе с этими реактивными потоками необходимо вызвать `subscribe()`, так как иначе ничего не произойдет.

В следующем примере показано, как подписаться на `Mono` и отобразить значение конфигурации в консоли.

```java
ConfigurationAsyncClient asyncClient = new ConfigurationClientBuilder()
    .connectionString("<your connection string>")
    .buildAsyncClient();

asyncClient.getConfigurationSetting("<your config key>", "<your config value>").subscribe(
    config -> System.out.println("Config value: " + config.getValue()),
    ex -> System.out.println("Error getting configuration: " + ex.getMessage()),
    () -> System.out.println("Successfully retrieved configuration setting"));

System.out.println("Done");
```

Обратите внимание, что после вызова `getConfigurationSetting()` в клиенте пример кода подписывается на результат и предоставляет три отдельных лямбда-выражения. Первое лямбда-выражение использует данные, полученные от службы, которая активируется после успешного ответа. Второе лямбда-выражение активируется при возникновении ошибки во время получения конфигурации. Третье лямбда-выражение вызывается после завершения потока данных. Это означает, что из этого потока больше не ожидается элементов данных.

> [!NOTE]
> Как и при любом асинхронном программировании, после создания подписки выполнение продолжается в обычном режиме. Если нет ничего, что поддерживало бы программу в активном состоянии и состоянии выполнения, ее работа может быть завершена до окончания асинхронной операции. Основной поток, вызвавший `subscribe()`, не будет ожидать, пока вы сделаете сетевой вызов службы "Конфигурация приложений Azure" и получите ответ. В производственных системах можно продолжить обработку других данных. Однако в этом примере можно добавить небольшую задержку. Для этого вызовите `Thread.sleep()` или используйте `CountDownLatch`, чтобы дать возможность асинхронной операции завершиться.

Как показано в следующем примере, интерфейсы API, возвращающие `Flux`, следуют аналогичному шаблону. Разница заключается в том, что первый обратный вызов, предоставленный методу `subscribe()`, вызывается несколько раз для каждого элемента данных в ответе. Ошибки или обратные вызовы завершения вызываются только один раз и считаются сигналами терминала. Если от издателя получен любой из этих сигналов, никакие другие обратные вызовы не выполняются.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(
    event -> System.out.println("Sequence number of received event: " + event.getData().getSequenceNumber()),
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));
```

### <a name="backpressure"></a>Обратная реакция

Что происходит, когда источник создает данные с более высокой скоростью, чем может обработать подписчик? Подписчик может получить переизбыток данных, что может привести к ошибкам нехватки памяти. Подписчику требуется способ обмена данными с издателем для замедления потока, когда его невозможно обработать. По умолчанию при вызове `subscribe()` для `Flux`, как показано в приведенном выше примере, подписчик запрашивает непривязанный поток данных, который указывает издателю, что данные требуется отправлять как можно быстрее. Такое поведение не всегда желательно, и подписчику может потребоваться управлять скоростью публикации с помощью "обратной реакции". Обратная реакция позволяет подписчику управлять потоком элементов данных. Подписчик запрашивает ограниченное число элементов данных, которые можно обработать. После того как подписчик завершит обработку этих элементов, он может запросить еще. С помощью обратной реакции можно преобразовать модель принудительной отправки в модель опрашивания и запрашивания для передачи данных.

В следующем примере показано, как можно контролировать скорость получения событий потребителем концентраторов событий.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.request(1); // request another event when the subscriber is ready
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

Когда подписчик впервые подключается к издателю, он передает подписчику экземпляр `Subscription`, который управляет состоянием передаваемых данных. Этот экземпляр `Subscription` является средой передачи, с помощью которой подписчик может применять обратную реакцию, вызывая `request()`, чтобы указать, сколько элементов данных может быть обработано.

Если подписчик запрашивает несколько элементов данных каждый раз при вызове `onNext()`, например `request(10)`, издатель немедленно отправит следующие 10 элементов, если они доступны (или когда станут доступны). Эти элементы накапливаются в буфере на стороне подписчика. Так как каждый вызов `onNext()` будет запрашивать следующие 10 элементов, невыполненная работа будет продолжать расти, пока у издателя не закончатся элементы данных для отправки или до переполнения буфера подписчика, что приведет к ошибкам нехватки памяти.

### <a name="cancel-a-subscription"></a>Отмена подписки

Подписка управляет состоянием передачи данными между издателем и подписчиком. Подписка активна до тех пор, пока издатель не завершит передачу всех данных подписчику или подписчик больше не захочет получать данные. Подписку можно отменить несколькими способами, как показано ниже.

В следующем примере подписка отменяется путем удаления подписчика.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

Disposable disposable = asyncClient.receive().subscribe(
    partitionEvent -> {
        Long num = partitionEvent.getData().getSequenceNumber()
        System.out.println("Sequence number of received event: " + num);
    },
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));

// much later on in your code, when you are ready to cancel the subscription,
// you can call the dispose method, as such:
disposable.dispose();
```

В следующем примере подписка отменяется путем вызова метода `cancel()` в `Subscription`.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.cancel(); // Cancels the subscription. No further event is received.
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

## <a name="conclusion"></a>Заключение

Потоки — это дорогостоящие ресурсы, которые не должны простаивать в ожидании ответов от вызовов удаленных служб. По мере увеличения архитектуры микрослужб необходимость масштабирования и эффективного использования ресурсов становится крайне важной. Асинхронные API-интерфейсы являются предпочтительными при наличии операций, связанных с сетью. Пакет SDK Azure для Java предоставляет обширный набор интерфейсов API для асинхронных операций, которые позволяют максимально увеличить объем системных ресурсов. Мы настоятельно рекомендуем попробовать и оценить асинхронные клиенты.

Дополнительные сведения об операторах, которые лучше подходят для конкретных задач, см. в [этом разделе](https://projectreactor.io/docs/core/release/reference/#which-operator) справочного руководства по работе с [Reactor 3](https://projectreactor.io/docs/core/release/reference/index.html).

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы лучше понимаете различные принципы асинхронного программирования, важно изучить, как выполнять итерацию по результатам. Дополнительные сведения о лучших стратегиях итерации и сведения о том, как работает разбиение на страницы, см. в статье [Разбиение на страницы и итерация в пакете SDK Azure для Java](pagination.md).
