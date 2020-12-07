---
title: Руководство для разработчиков Spring Data Azure Cosmos DB
description: В этом руководстве рассказывается, о чем следует знать при использовании пакета SDK для Spring Data Azure Cosmos DB.
author: anfeldma-ms
ms.author: anfeldma
ms.topic: conceptual
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 45872eba2fd6929cf406df4a551559fd5ad78c40
ms.sourcegitcommit: 63732132cb88206b99876f0bcd035b52c301f315
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523139"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Руководство для разработчиков Spring Data Azure Cosmos DB

В этом разделе описываются возможности [Spring Data Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) при использовании API SQL. В этом разделе также описываются распространенные проблемы, рекомендации по их устранению и шаги диагностики.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) — это глобально распределенная служба базы данных, предоставляющая разработчикам возможность работать с данными с помощью разных стандартных API. Пакет SDK для Spring Data Azure Cosmos DB основан на платформе [Spring Data](https://spring.io/projects/spring-data). Он обеспечивает интеграцию с Azure Cosmos DB с помощью API SQL. Сведения о поддержке других API можно найти в следующих разделах:

- [Как использовать API MongoDB Spring Data с Azure Cosmos DB](./configure-spring-data-mongodb-with-cosmos-db.md)
- [Как использовать API Apache Cassandra Spring Data с Azure Cosmos DB](./configure-spring-data-apache-cassandra-with-cosmos-db.md)
- [Как использовать начальное приложение Spring Data Gremlin с API SQL для Azure Cosmos DB](./configure-spring-data-gremlin-java-app-with-cosmos-db.md)

Пакет SDK для Spring Data Azure Cosmos DB доступен в GitHub в репозитории [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) как ПО с открытым кодом. В этом репозитории доступен активный список [Issues](https://github.com/Azure/azure-sdk-for-java/issues) (Проблемы), в который можно внести ошибки или проверить наличие решений для проблем, которые уже внесены. Можно также просмотреть список [Releases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md) (Выпуски), чтобы узнать, устранена ли проблема в более новой версии. 

## <a name="available-features"></a>Доступные функции

В следующих разделах описаны функции, доступные в настоящее время.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Поддержка CrudRepository и ReactiveCrudRepository

В пакете SDK для Spring Data Azure Cosmos DB предоставляются интерфейсы `CosmosRepository` и `ReactiveCosmosRepository`, расширяющие возможности интерфейсов Spring Data `CrudRepository` и `ReactiveCrudRepository`.

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
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>определение простой сущности

Сущности можно определить, добавив заметку `@Container` и указав свойства, связанные с коллекцией, такие как имя коллекции, единицы запроса (ЕЗ), срок жизни и флаг автоматического создания коллекции.

По умолчанию именем коллекции будет имя пользовательского доменного класса. Чтобы настроить его, добавьте в доменный класс заметку `@Container(containerName="myCustomCollectionName")`. Поле `containerName` также поддерживает выражения языка [Spring Expression Language](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (SpEL), поэтому имена коллекций можно указать программно с помощью свойств конфигурации. Например, можно использовать такие выражения, как `containerName = "${dynamic.container.name}"` и `containerName = "#{@someBean.getContainerName()}"`.

Существуют два способа сопоставления поля в доменном классе с полем `id` документа Azure Cosmos DB.

- Можно добавить к полю заметку с `@Id`.
- Можно присвоить полю имени значение `id`.

В следующем примере показано использование заметок `@Container` и `@Id`.

```java
@Container(containerName = "myContainer")
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

Пакет SDK также поддерживает секционирование. Дополнительные сведения см. в статье [Секционирование и горизонтальное масштабирование в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#:~:text=%20Partitioning%20and%20horizontal%20scaling%20in%20Azure%20Cosmos,partitions.%20Azure%20Cosmos%20DB%20transparently%20and...%20More%20). Чтобы указать поле доменного класса в качестве поля ключа секции, добавьте к нему заметку `@PartitionKey`. Затем при выполнении операций CRUD указывайте значение секции.

В следующем примере показано, как использовать заметку `@PartitionKey` при выполнении операций CRUD.

```java
@Container(ru = "400")
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

    final Address newAddress = new Address("12345", "Seattle");

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

### <a name="pulling-configuration-properties-into-the-application"></a>Извлечение свойств конфигурации в приложение

Вы можете создать класс свойств, который предоставляет параметры **application.properties** как методы доступа Java. Структура **application.properties** может иметь следующий вид:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Создав зеркальное отображение этой структуры, создайте класс Java `CosmosProperties` со следующей структурой:

```java
@ConfigurationProperties(prefix = "cosmos")
public class CosmosProperties {

    private String uri;

    private String key;

    private String secondaryKey;

    private boolean queryMetricsEnabled;

    public String getUri() {
        return uri;
    }

    public void setUri(String uri) {
        this.uri = uri;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getSecondaryKey() {
        return secondaryKey;
    }

    public void setSecondaryKey(String secondaryKey) {
        this.secondaryKey = secondaryKey;
    }

    public boolean isQueryMetricsEnabled() {
        return queryMetricsEnabled;
    }

    public void setQueryMetricsEnabled(boolean enableQueryMetrics) {
        this.queryMetricsEnabled = enableQueryMetrics;
    }
}
```

Обратите внимание, что этот класс содержит элемент, соответствующий каждому свойству конфигурации **application.properties**, и что для каждого элемента `CosmosProperties` предоставляет методы *get* и *set*. Заметка `@ConfigurationProperties` определяет класс как представляющий свойства конфигурации, а аргумент `prefix = "cosmos"` указывает, что заданный *элемент* `CosmosProperties` сопоставляется со свойством `cosmos.member` в **application.properties**.

В следующем разделе мы покажем, как внедрить класс `CosmosProperties` в автоматизированную последовательность настройки. Во время настройки будет создан экземпляр `CosmosProperties`, а его методы будут заполнены параметрами конфигурации в **application.properties**. Этот экземпляр свойств позволяет приложению считывать и изменять свойства конфигурации во время выполнения.

### <a name="configuring-the-application-based-on-properties"></a>Настройка приложения на основе свойств

Следующий шаг заключается в создании класса конфигурации, который автоматизирует настройку приложения. Ниже приведен соответствующий пример, который мы рассмотрим:

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class AppConfiguration extends AbstractCosmosConfiguration {

    private static final Logger logger = LoggerFactory.getLogger(QuickstartSampleConfiguration.class);

    @Autowired
    private CosmosProperties properties;

    private AzureKeyCredential azureKeyCredential;

    @Bean
    public CosmosClientBuilder cosmosClientBuilder() {
        this.azureKeyCredential = new AzureKeyCredential(properties.getKey());
        return new CosmosClientBuilder()
            .endpoint(properties.getUri())
            .key(this.azureKeyCredential)
    }

    @Bean
    public CosmosConfig cosmosConfig() {
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig();        
        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig);                           
                           .build();
    }

    @Override
    protected String getDatabaseName() {
        return "testdb";
    }

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            logger.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Чтобы создать структуру класса конфигурации, выполните следующие действия:

1. Расширьте класс `AbstractCosmosConfiguration`, чтобы настроить конфигурацию приложения (ключ Cosmos DB, URL-адрес, имя базы данных и т. д.).
1. Добавьте заметку `@Configuration`.
1. В зависимости от используемого репозитория добавьте одну или обе заметки `@EnableCosmosRepositories` и `@EnableReactiveCosmosRepositories`.
1. Добавьте заметку `@PropertySource("classpath:application.properties")`, которая сигнализирует об извлечении пары "ключ — значение" свойств из **application.properties**.
1. Добавьте заметку `@EnableConfigurationProperties`, указывающую Spring Data на класс, который может хранить пары "ключ — значение", из **application.properties**. Эта заметка принимает определение класса в качестве аргумента, поэтому передайте `CosmosProperties.class`.

Класс конфигурации будет использовать следующие элементы:

1. Объявите и определите элемент log4j2 `logger`, который будет использоваться Spring Data для всех выходных данных журналов.
1. Объявите элемент `@Autowired` `CosmosProperties`, **в который будут помещены параметры application.properties**.

Функция `AzureKeyCredential` позволяет динамически менять ключи. Чтобы включить эту функцию, определите элемент `AzureKeyCredential`. Ключи можно переключить, добавив метод `switchToSecondaryKey`, как показано в примере выше.

Далее необходимо определить, как должна выполняться автоматическая настройка.
1. Определите метод `@Bean` `cosmosClientBuilder` для управления инициализацией клиента с помощью `CosmosClientBuilder`. Этот метод предназначен для выполнения базовой настройки клиента, т. е. для указания URI конечной точки учетной записи и ключа доступа. Обычно URI конечной точки учетной записи и ключ доступа определяются в файле **application.properties**, значения из которого также переносятся в `properties`. Вы можете инициализировать элемент `azureKeyCredential` с помощью функции `properties.getKey()`, а затем передать `properties.getUri()` и `this.azureKeyCredential` в методы построителя `endpoint` и `key` соответственно. 

Обратите внимание, что в приведенном выше примере `cosmosClientBuilder` не вызывает `build()` для построителя клиента — он возвращает незавершенную структуру построителя. Spring Data позволяет выполнять настройку в два этапа: сначала `cosmosClientBuilder` может применить базовую конфигурацию и вернуть структуру конфигурации, а затем Spring Data вызовет метод `cosmosConfig`, который позволяет определить более сложную конфигурацию, например метрики и параметры диагностики. Далее мы рассмотрим эту расширенную конфигурацию в методе `cosmosConfig`:
1. Создайте метод `@Bean` `cosmosConfig`, как показано выше.
1. Azure Cosmos DB может возвращать данные диагностики на стороне сервера по каждому запросу. Spring Data позволяет преобразовывать необработанные выходные данные диагностики перед их регистрацией в журнале, определяя обработчик данных диагностики клиента. Определите класс (как показано выше), который реализует `ResponseDiagnosticsProcessor` и переопределяет метод `processResponseDiagnostics`. Вы можете определить `processResponseDiagnostics` для управления тем, как обрабатываются выходные данные диагностики. В приведенном выше примере необработанные данные диагностики просто записываются в журнал.
1. Чтобы включить диагностику и инициализировать обработчик данных диагностики, вызовите метод построителя `responseDiagnosticsProcessor`, передав новый экземпляр класса обработчика клиента:

    ```java
    return CosmosConfig.builder()
                       .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
    ```
1. Azure Cosmos DB также имеет специализированные функции метрик производительности для запросов, которые называются метриками запросов. Как показано в предыдущем разделе, в **application.properties** рекомендуется иметь параметр, который включает или отключает метрики запросов. Примените этот параметр конфигурации, добавив метод построителя `.enableQueryMetrics(properties.isQueryMetricsEnabled())` в `cosmosConfig`.
1. Рекомендуется использовать подключение в прямом режиме для минимальной задержки и максимальной пропускной способности, поэтому вы также можете настроить его в построителе клиента.

После завершения расширенной настройки в `cosmosConfig` необходимо активировать создание клиента, вызвав `build()` в структуре конфигурации. Это приведет к созданию экземпляра клиента Azure Cosmos DB на основе ваших параметров конфигурации.

Последним шагом в определении процесса настройки будет добавление метода `@Override` `getDatabaseName()`, который возвращает имя базы данных Azure Cosmos DB в виде строки.

### <a name="customizing-the-configuration"></a>Настройка конфигурации

Можно также настроить конфигурацию, чтобы изменить режим подключения, максимальный размер пула подключений, время ожидания запроса и т. д., как показано в следующем примере.

```java
    @Bean
    public CosmosConfig cosmosConfig() {

        // Set the connection mode to Direct (TCP) which applies to data plane operations
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig(); 

        // Even in Direct mode, some control plane operations always pass through the gateway as HTTP requests (i.e. container/database CRUD.)
        // Optionally, you can customize connection properties for these specific operations which are
        // always Gateway mode
        GatewayConnectionConfig gatewayConnectionConfig = GatewayConnectionConfig.getDefaultConfig(); 

        // Set the maximum number of HTTP connections to 1000 per application.
        gatewayConnectionConfig.setMaxConnectionPoolSize(1000);

        // Set the request timeout to 10 seconds.
        gatewayConnectionConfig.setIdleConnectionTimeout(Duration.ofMillis(10000));

        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig, gatewayConnectionConfig); // directMode() has an override which accepts Gateway config                        
                           .build();
    }
```

### <a name="response-diagnostics-and-query-metrics"></a>Диагностика ответов и метрики запросов

Пакет SDK для Spring Data Azure Cosmos DB поддерживает строку диагностики ответов и метрики запросов, начиная с версии 2.

Чтобы включить метрики запросов, в файле `application.properties` установите для флага `queryMetricsEnabled` значение **true**. Затем выполните инструкции из предыдущего раздела, чтобы расширить интерфейс `ResponseDiagnosticsProcessor` и реализовать метод `processResponseDiagnostics` для записи диагностических сведений в журнал. Наконец, передайте экземпляр реализации в метод `CosmosDbConfig.setResponseDiagnosticsProcessor`.

### <a name="pagination-and-sorting"></a>Разбиение на страницы и сортировка

Пакет SDK для Spring Data Azure Cosmos DB поддерживает разбиение на страницы и сортировку данных Spring Data. Дополнительные сведения см. в разделе [Special parameter handling](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters) (Специальная обработка параметров) в документации по Spring.

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

В следующих разделах описаны проблемы, о которых следует знать при использовании пакета SDK для Spring Data Azure Cosmos DB.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Получение правильной конфигурации Cosmos DB

Расширение интерфейса `AbstractCosmosConfiguration` может быть непростой задачей из-за разнообразных заметок и конфигураций, имеющихся в классе. Чаще всего проблемы связаны с заметкой `Enable Repositories`.

Если репозитории расширяют `CosmosRepository`, обязательно добавьте заметку `@EnableCosmosRepositories`. Если репозитории расширяют `ReactiveCosmosRepository`, обязательно добавьте заметку `@EnableReactiveCosmosRepositories`. В следующем примере показано использование этих заметок.

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

При создании или настройке bean-компонента `CosmosDBConfig` следует использовать объект `AzureKeyCredential`, а не напрямую указывать ключ.

Функция `AzureKeyCredential` позволяет динамически менять ключи. Ключи можно переключать с помощью метода `switchToSecondaryKey`.

`AzureKeyCredential` должен быть singleton-объектом, так как пакет SDK для Cosmos DB использует один и тот же объект для обнаружения изменений значения ключа внутри этого объекта.

### <a name="custom-query-execution"></a>Выполнение настраиваемых запросов

Пакет SDK Spring Data Azure Cosmos DB версии 3.x.x позволяет использовать заметку `@query` для определения пользовательских запросов.

В следующем коде показан простой пример определения запросов offset и limit с помощью заметки `@query`:

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {

    ...

    @Query(value = "SELECT * from c OFFSET @skipCount LIMIT @takeCount")
    List<SampleEntity> findByName(@Param("skipCount") int skipCount, @Param("takeCount") int takeCount);
}
```

### <a name="enable-diagnostics-and-query-metrics"></a>Включение диагностики и метрик запросов

При отладке удобно использовать строку диагностики ответов и метрики запросов из пакета SDK для Cosmos DB. Пакет SDK для Cosmos DB записывает строку диагностики ответов на стороне клиента. Серверная часть записывает данные метрик запросов и предоставляет их пакету SDK для Cosmos DB.

Метод `ResponseDiagnosticsProcessor.processResponseDiagnostics` вызывается после каждого вызова API в пакете SDK для Spring Data Azure Cosmos DB. Поэтому важно, чтобы ваша реализация обеспечивала высокую производительность за счет отсутствия ошибок и излишней сложности. Например, не следует записывать в журнал полный набор диагностических сведений в этом методе, так как этот объем информации значительно повлияет на производительность. Кроме того, следует использовать уровень ведения журнала `Debug`, чтобы не влиять на производительность приложения.

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

### <a name="naming-changes"></a>Изменение имен.

В версии пакета SDK для Spring Data Azure Cosmos DB 3.1.0 и выше реализованы следующие важные изменения в именах и интерфейсах классов, методов, заметок и артефактов Maven:
* Идентификатор группы изменен на `com.azure`.
* Идентификатор артефакта изменен на `azure-spring-data-cosmos`.
* Типы возвращаемых значений API синхронизации изменены на типы `Iterable` (вместо `List`).
* `CosmosDbFactory` изменено на `CosmosFactory`.
* `CosmosDBConfig` изменено на `CosmosConfig`.
* `CosmosDBAccessException` изменено на `CosmosAccessException`.
* Заметка `Document` изменена на заметку `Container`.
* Заметка `DocumentIndexingPolicy` изменена на заметку `CosmosIndexingPolicy`.
* `DocumentQuery` изменено на `CosmosQuery`.
* Флаг **application.properties** `populateQueryMetrics` изменен на `queryMetricsEnabled`.

### <a name="key-bug-fixes"></a>Основные исправления ошибок

В версии пакета SDK Spring Data Azure Cosmos DB 3.1.0 и выше реализованы следующие основные исправления ошибок.
* Исправлена проблема, из-за которой запросы с заметками не принимали имя контейнера с заметкой.
* Планирование задачи ведения журнала диагностики теперь выполняется с параллельными потоками, что предотвращает блокировку потоков ввода-вывода Netty.
* Исправлена проблема с оптимистической блокировкой для операции удаления.
* Исправлена проблема с экранированием запросов для предложения IN.
* Исправлена проблема с разрешением типа данных long для @Id.
* Исправлена проблема с разрешением типов данных boolean, long, int и double для заметки @PartitionKey.
* Исправлены ключевые слова IgnoreCase и AllIgnoreCase для игнорирования запросов регистра.
* Удалено значение единицы запроса по умолчанию (4000) при автоматическом создании контейнеров.
* Исправлена ошибка вложенного ключа секции при использовании с заметкой @GeneratedValue.

### <a name="api-or-query-slowness"></a>Медленная работа API или запросов

Если у вас возникают большие задержки в работе API или выполнении запросов, попробуйте вести журнал строк диагностики и метрик запросов, как описано в разделе [Включение диагностики и метрик запросов](#enable-diagnostics-and-query-metrics). Проверьте данные об использовании ЦП, пропускной способности сети и дискового пространства ввода-вывода, которые могут являться первопричиной медленной работы на стороне клиента.