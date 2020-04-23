---
title: Руководство для разработчиков Spring Data Azure Cosmos DB
description: В этом руководстве рассказывается, о чем следует знать при использовании пакета SDK для Spring Data Azure Cosmos DB.
author: kushagraThapar
ms.author: kuthapar
ms.topic: conceptual
ms.date: 1/9/2019
ms.openlocfilehash: 838fb4efa79f5d3ef8a97977a0d239a809e2506d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81674290"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Руководство для разработчиков Spring Data Azure Cosmos DB

В этом разделе описываются возможности [Spring Data Cosmos DB](https://github.com/microsoft/spring-data-cosmosdb) при использовании API SQL. В этом разделе также описываются распространенные проблемы, рекомендации по их устранению и шаги диагностики.

[Azure Cosmos DB](/azure/cosmos-db/introduction) — это глобально распределенная служба базы данных, предоставляющая разработчикам возможность работать с данными с помощью разных стандартных API. Пакет SDK для Spring Data Cosmos DB основан на платформе [Spring Data](https://spring.io/projects/spring-data). Он обеспечивает интеграцию с Azure Cosmos DB с помощью API SQL. Сведения о поддержке других API можно найти в следующих разделах:

- [Как использовать API MongoDB Spring Data с Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
- [Как использовать API Apache Cassandra Spring Data с Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
- [Как использовать начальное приложение Spring Data Gremlin с API SQL для Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)

Пакет SDK для Spring Data Cosmos DB доступен на сайте GitHub в репозитории [spring-data-cosmosdb](https://github.com/microsoft/spring-data-cosmosdb) как ПО с открытым кодом. В этом репозитории доступен активный список [Issues](https://github.com/microsoft/spring-data-cosmosdb/issues) (Проблемы), в который можно внести ошибки или проверить наличие решений для проблем, которые уже внесены. Можно также просмотреть список [Releases](https://github.com/microsoft/spring-data-cosmosdb/releases) (Выпуски), чтобы узнать, устранена ли проблема в более новой версии. Цепочка выпусков пакета SDK для Spring Data Cosmos DB версии 2.2.x поддерживает spring-data-commons версии 2.2.0.RELEASE, а цепочка выпусков этого пакета SDK версии 2.1.x поддерживает spring-data-commons версии 2.1.0.RELEASE.

## <a name="available-features"></a>Доступные функции

В следующих разделах описаны функции, доступные в настоящее время.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Поддержка CrudRepository и ReactiveCrudRepository

В пакете SDK для Spring Data Cosmos DB предоставляются интерфейсы `CosmosRepository` и `ReactiveCosmosRepository`, расширяющие возможности интерфейсов Spring Data `CrudRepository` и `ReactiveCrudRepository`.

В следующем примере показано, как расширить возможности этих интерфейсов.

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {
    List<SampleEntity> findByName(String name);
}

@Repository
public interface ReactiveSampleRepository extends ReactiveCosmosRepository<SampleEntity, String> {
    Flux<SampleEntity> findByName(String name);
}
```

В зависимости от применения необходимо отдельно добавить оба репозитория в класс `Configuration`. Пример:

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>определение простой сущности

Сущности можно определить, добавив заметку `@Document` и указав свойства, связанные с коллекцией, такие как имя коллекции, единицы запроса (ЕЗ), срок жизни и флаг автоматического создания коллекции.

По умолчанию именем коллекции будет имя пользовательского доменного класса. Чтобы настроить его, добавьте в доменный класс заметку `@Document(collection="myCustomCollectionName")`. Поле коллекции также поддерживает выражения языка [Spring Expression Language](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (SpEL), поэтому имена коллекций можно указать программно с помощью свойств конфигурации. Например, можно использовать такие выражения, как `collection = "${dynamic.collection.name}"` и `collection = "#{@someBean.getCollectionName()}"`.

Существуют два способа сопоставления поля в доменном классе с полем `id` документа Azure Cosmos DB.

- Можно добавить к полю заметку с `@Id`.
- Можно присвоить полю имени значение `id`.

В следующем примере показано использование заметок `@Document` и `@Id`.

```java
@Document(collection = "myCollection")
class MyDocument {

    @Id
    private String myId;

    @PartitionKey
    private String data;

    @Version
    private String _etag;
}
```

По умолчанию `IndexingPolicy` задается службой Azure. Чтобы настроить его, добавьте в доменный класс заметку `@DocumentIndexingPolicy`. Эта заметка имеет четыре атрибута:

```java
boolean automatic;     // Indicates whether the indexing policy is automatic.
IndexingMode mode;     // The indexing policy mode; the options are Consistent, Lazy, or None.
String[] includePaths; // Included paths for indexing.
String[] excludePaths; // Excluded paths for indexing.
```

Пакет SDK также поддерживает секционирование. Дополнительные сведения см. в статье [Секционирование и горизонтальное масштабирование в Azure Cosmos DB](/azure/cosmos-db/partition-data). Чтобы указать поле доменного класса в качестве поля ключа секции, добавьте к нему заметку `@PartitionKey`. Затем при выполнении операций CRUD указывайте значение секции.

В следующем примере показано, как использовать заметку `@PartitionKey` при выполнении операций CRUD.

```java
@Document(ru = "400")
public class Address {
    @Id
    String postalCode;

    @PartitionKey
    String city;

    String street;
    String country;
    String phoneNumber;

    ...
}

class AddressService {

    @Autowired
    AddressRepository repository;

    final Address newAddress = new Address("12345", "city");

    // There's no need to specify a partition key in the save operation.
    repository.save(updatedAddress);

    // Provide a partition key when performing a find-by-id operation.
    final Optional<Address> addressById = repository.findById("12345", new PartitionKey("city"));

    final Address foundAddress = addressById.get();

    // Provide a partition key when performing a delete-by-id operation.
    repository.deleteById(foundAddress.getPostalCode(), new PartitionKey(foundAddress.getCity())); 
}
```

Пакет SDK также поддерживает операции поиска с настраиваемыми запросами Spring Data, например `findByAFieldAndBField`. Дополнительные сведения см. в разделе [Defining Query Methods](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details) (Определение методов запроса) в документации по Spring Data.

## <a name="best-practices"></a>Рекомендации

В следующих разделах описаны рекомендации по использованию пакета SDK.

### <a name="configuring-the-application"></a>Настройка приложения

Чтобы настроить приложение, выполните следующие действия.

1. Расширьте класс `AbstractCosmosConfiguration`, чтобы настроить конфигурацию приложения (ключ Cosmos DB, URL-адрес, имя базы данных и т. д.).
1. Добавьте заметку `@Configuration`.
1. В зависимости от используемого репозитория добавьте одну или обе заметки `@EnableCosmosRepositories` и `@EnableReactiveCosmosRepositories`.

Функция `CosmosKeyCredential` позволяет динамически менять ключи. Ключи можно переключать с помощью метода `switchToSecondaryKey`.

В следующем примере кода показана конфигурация приложения и демонстрируется использование `switchToSecondaryKey`.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    @Value("${azure.cosmosdb.uri}")
    private String uri;

    @Value("${azure.cosmosdb.key}")
    private String key;

    @Value("${azure.cosmosdb.secondaryKey}")
    private String secondaryKey;

    @Value("${azure.cosmosdb.database}")
    private String dbName;

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private CosmosKeyCredential cosmosKeyCredential;

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri,
            this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Можно также настроить конфигурацию, чтобы изменить режим подключения, максимальный размер пула подключений, время ожидания запроса и т. д., как показано в следующем примере.

```java
public CosmosDBConfig getConfig() {

    this.cosmosKeyCredential = new CosmosKeyCredential(key);
    ConnectionPolicy customizedConnectionPolicy = new ConnectionPolicy();

    // Set the connection mode to Direct (TCP).
    customizedConnectionPolicy.setConnectionMode(ConnectionMode.DIRECT);

    // Set the maximum number of HTTP/TCP connections to 1000 per application.
    customizedConnectionPolicy.setMaxPoolSize(1000);

    // Set the request timeout to 10 seconds.
    customizedConnectionPolicy.requestTimeoutInMillis(10000);

    // Set the idle connection timeout to two minutes.
    customizedConnectionPolicy.idleConnectionTimeoutInMillis(120000);
    CosmosDBConfig cosmosDbConfig = CosmosDBConfig.builder(uri,   this.cosmosKeyCredential, dbName)
                                                  .connectionPolicy  (customizedConnectionPolic  y)
                                                  .build();
    return cosmosDbConfig;
}
```

### <a name="response-diagnostics-and-query-metrics"></a>Диагностика ответов и метрики запросов

Версия 2.2.x пакета SDK для Spring Data Cosmos DB поддерживает строку диагностики ответов и метрики запросов.

Чтобы включить метрики запросов, в файле `application.properties` установите для флага `populateQueryMetrics` значение **true**. Затем расширьте интерфейс `ResponseDiagnosticsProcessor` и реализуйте метод `processResponseDiagnostics` для ведения журнала диагностики. Наконец, передайте экземпляр реализации в метод `CosmosDbConfig.setResponseDiagnosticsProcessor`. Ниже приведен код примера реализации.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    ...

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            log.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri, this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }
}
```

### <a name="pagination-and-sorting"></a>Разбиение на страницы и сортировка

Пакет SDK для Spring Data Cosmos DB поддерживает разбиение на страницы и сортировку данных Spring Data. Дополнительные сведения см. в разделе [Special parameter handling](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters) (Специальная обработка параметров) в документации по Spring.

В зависимости от единиц запроса (ЕЗ), доступных для учетной записи базы данных, Cosmos DB может возвращать документы, размер которых меньше или равен запрошенному размеру. См. дополнительные сведения о [единицах запросов в базе данных Azure Cosmos DB](/azure/cosmos-db/request-units).

Не следует полагаться на значение `totalPageSize`, так как число возвращаемых документов в каждой итерации является переменным. Вместо этого необходимо выполнить итерацию по объекту `Pageable`, как показано в следующем примере.

```java
final Sort sort = Sort.by(Sort.Direction.DESC, "name");
final CosmosPageRequest pageRequest = new CosmosPageRequest(0, pageSize,   null, sort);
Page<T> page = tRepository.findAll(pageRequest);
List<T> pageContent = page.getContent();
while(page.hasNext()) {
    Pageable nextPageable = page.nextPageable();
    page = repository.findAll(nextPageable);
    pageContent = page.getContent();
}
```

## <a name="common-issues-and-workarounds"></a>Распространенные проблемы и обходные решения для них

В следующих разделах описаны проблемы, о которых следует знать при использовании пакета SDK для Spring Data Cosmos DB.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Получение правильной конфигурации Cosmos DB

Расширение интерфейса `AbstractCosmosConfiguration` может быть непростой задачей из-за разнообразных заметок и конфигураций, имеющихся в классе. Чаще всего проблемы связаны с заметкой `Enable Repositories`.

Если репозитории расширяют `CosmosRepository`, обязательно добавьте заметку `@EnableCosmosRepositories`. Если репозитории расширяют `ReactiveCosmosRepository`, обязательно добавьте заметку `@EnableReactiveCosmosRepositories`. В следующем примере показано использование этих заметок.

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

При создании или настройке bean-компонента `CosmosDBConfig` следует использовать объект `CosmosKeyCredential`, а не напрямую указывать ключ.

Функция `CosmosKeyCredential` позволяет динамически менять ключи. Ключи можно переключать с помощью метода `switchToSecondaryKey`.

`CosmosKeyCredential` должен быть singleton-объектом, так как пакет SDK для Cosmos DB использует один и тот же объект для обнаружения изменений значения ключа внутри этого объекта.

### <a name="custom-query-execution"></a>Выполнение настраиваемых запросов

Функция добавления заметки к запросу еще не поддерживается пакетом SDK для Spring Data Cosmos DB. Пока что вы можете выполнять настраиваемые и сложные запросы непосредственно с bean-компонентом `cosmosClient`, доступным в контексте приложения Spring.

В следующем коде показан простой пример выполнения запросов offset и limit с помощью bean-компонента `cosmosClient`.

```java
final FeedOptions feedOptions = new FeedOptions();

// Enable cross-partition queries.
feedOptions.enableCrossPartitionQuery(true);

// Set the page size.
feedOptions.maxItemCount(20);

// Set the number of parallel operations on the client-side SDK when executing parallel queries.
feedOptions.maxDegreeOfParallelism(2);

// Populate query metrics from Cosmos DB.
feedOptions.populateQueryMetrics(true);

final String query = "SELECT * from c OFFSET " + skipCount + " LIMIT " + takeCount;

final CosmosClient cosmosClient = applicationContext.getBean(CosmosClient.class);

Flux<FeedResponse<CosmosItemProperties>> feedResponseFlux =
    cosmosClient.getDatabase(databaseId)
                .getContainer(collectionId)
                .queryItems(query, feedOptions);
    feedResponseFlux.subscribeOn(Schedulers.parallel())
                    .flatMap(feedResponse -> {
                        List<CosmosItemProperties> results =
                        feedResponseFlux.results();
                        log.info("Results are {}", results);
                        return feedResponse;
                    })
                    .subscribe();
```

### <a name="enable-diagnostics-and-query-metrics"></a>Включение диагностики и метрик запросов

При отладке удобно использовать строку диагностики ответов и метрики запросов из пакета SDK для Cosmos DB. Пакет SDK для Cosmos DB записывает строку диагностики ответов на стороне клиента. Серверная часть записывает данные метрик запросов и предоставляет их пакету SDK для Cosmos DB.

Метод `ResponseDiagnosticsProcessor.processResponseDiagnostics` вызывается после каждого вызова API в пакете SDK для Spring Data Cosmos DB. Поэтому важно, чтобы ваша реализация обеспечивала высокую производительность за счет отсутствия ошибок и излишней сложности. Например, не следует записывать в журнал полный набор диагностических сведений в этом методе, так как этот объем информации значительно повлияет на производительность. Кроме того, следует использовать уровень ведения журнала `Debug`, чтобы не влиять на производительность приложения.

В следующем фрагменте кода приведен пример реализации интерфейса `ResponseDiagnosticsProcessor`.

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log Cosmos DB response diagnostics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Cosmos DB response diagnostics {}", cosmosResponseDiagnostics);
        }

        // To log just the request latency:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Request latency {}", cosmosResponseDiagnostics.requestLatency());
        }

        // To log query metrics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            FeedResponseDiagnostics feedResponseDiagnostics =
                responseDiagnostics.getFeedResponseDiagnostics();
            log.debug("Query metrics {}", feedResponseDiagnostics);
        }
    }
}
```

## <a name="how-to-troubleshoot"></a>Как устранять неполадки

В следующих разделах описаны способы устранения распространенных проблем.

### <a name="connection-issues"></a>Проблемы с подключением

При возникновении проблем с подключением убедитесь, что в классе конфигурации все обязательные заметки указаны и верны, как описано в разделе [Получение правильной конфигурации Cosmos DB](#getting-the-correct-cosmos-db-configuration).

### <a name="api-exceptions"></a>Исключения API

Версия 2.2.1 пакета SDK для Spring Data Cosmos DB обеспечивает приведенные ниже усовершенствования обработки исключений.

- Все интерфейсы API порождают `CosmosDBAccessException`, который предоставляет поле `cosmosClientException` через метод получения.
- Пакет SDK для Cosmos DB создает `CosmosClientException`, который можно использовать для реализации любой логики повторных попыток на стороне клиента.
- Распространенные исключения при повторных попытках — это исключения с сообщениями `Resource already exists`, `Request rate too large`, `Request timeout exception` и т. д.

### <a name="api-or-query-slowness"></a>Медленная работа API или запросов

Если у вас возникают большие задержки в работе API или выполнении запросов, попробуйте вести журнал строк диагностики и метрик запросов, как описано в разделе [Включение диагностики и метрик запросов](#enable-diagnostics-and-query-metrics). Проверьте данные об использовании ЦП, пропускной способности сети и дискового пространства ввода-вывода, которые могут являться первопричиной медленной работы на стороне клиента.
