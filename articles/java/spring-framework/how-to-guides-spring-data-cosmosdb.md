---
title: Руководство для разработчиков Spring Data Azure Cosmos DB
description: В этом руководстве описаны функции, проблемы, решения и диагностические действия, о которых следует знать при использовании пакета SDK для Spring Data Azure Cosmos DB.
author: anfeldma-ms
ms.author: anfeldma
ms.topic: conceptual
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 725754dc55198c38e6a9148ed0e37b83bf8a294e
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394011"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Руководство для разработчиков Spring Data Azure Cosmos DB

В этой статье описаны возможности [Spring Data Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) при использовании API SQL, а также распространенные проблемы, рекомендации по их устранению и действия диагностики.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) — это глобально распределенная служба базы данных, с помощью которой разработчики могут работать с данными, используя разные стандартные API-интерфейсы. Пакет SDK для Spring Data Azure Cosmos DB основан на платформе [Spring Data](https://spring.io/projects/spring-data). Он обеспечивает интеграцию с Azure Cosmos DB с использованием API SQL. Дополнительные сведения о поддержке других API-интерфейсов см. в следующих статьях:

- [Как использовать API MongoDB Spring Data с Azure Cosmos DB](./configure-spring-data-mongodb-with-cosmos-db.md)
- [Как использовать API Apache Cassandra Spring Data с Azure Cosmos DB](./configure-spring-data-apache-cassandra-with-cosmos-db.md)
- [Использование начального приложения Spring Data Gremlin с API SQL Azure Cosmos DB](./configure-spring-data-gremlin-java-app-with-cosmos-db.md)

Пакет SDK для Spring Data Azure Cosmos DB доступен в GitHub в репозитории [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) как ПО с открытым кодом. В этом репозитории доступен активный [список проблем](https://github.com/Azure/azure-sdk-for-java/issues), в который можно внести сведения об ошибках или проверить наличие решений для проблем, которые уже внесены. Кроме того, вы можете просмотреть [список выпусков](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md), чтобы узнать, устранена ли проблема в более новой версии. 

## <a name="available-features"></a>Доступные функции

В следующих разделах описаны функции, которые сейчас доступны в пакете SDK для Spring Data Azure Cosmos DB.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Поддержка CrudRepository и ReactiveCrudRepository

В пакете SDK для Spring Data Azure Cosmos DB предоставляются интерфейсы `CosmosRepository` и `ReactiveCosmosRepository`, расширяющие возможности интерфейсов Spring Data `CrudRepository` и `ReactiveCrudRepository`.

В следующем примере показано, как расширить возможности этих интерфейсов:

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

В зависимости от предполагаемого использования необходимо включить каждый репозиторий отдельно в классе `Configuration`. Пример:

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

По умолчанию имя коллекции — это имя пользовательского доменного класса. Чтобы настроить его, добавьте в доменный класс заметку `@Container(containerName="myCustomCollectionName")`. Поле `containerName` также поддерживает выражения языка [Spring Expression Language](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (SpEL), поэтому имена коллекций можно указать программно с помощью свойств конфигурации. Например, можно использовать такие выражения, как `containerName = "${dynamic.container.name}"` и `containerName = "#{@someBean.getContainerName()}"`.

Поле в доменном классе можно сопоставлять с полем `id` документа Azure Cosmos DB одним из двух способов:

- Можно добавить к полю заметку с `@Id`.
- Можно присвоить полю имени значение `id`.

Использование заметок `@Container` и `@Id` показано в следующих примерах:

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
IndexingMode mode;     // The indexing policy mode. The options are Consistent, Lazy, or None.
String[] includePaths; // The included paths for indexing.
String[] excludePaths; // The excluded paths for indexing.
```

Пакет SDK поддерживает секционирование. Дополнительные сведения см. в статье [Секционирование и горизонтальное масштабирование в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview). Чтобы указать поле доменного класса в качестве поля ключа секции, добавьте к нему заметку `@PartitionKey`. Затем при выполнении операций CRUD указывайте значение секции.

В приведенном ниже примере показано, как использовать заметку `@PartitionKey` при выполнении операций CRUD.

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

Пакет SDK также поддерживает операции поиска с настраиваемыми запросами Spring Data, например `findByAFieldAndBField`. Дополнительные сведения см. в разделе Defining Query Methods (Определение методов запроса) [документации по Spring Data](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details).

## <a name="best-practices"></a>Рекомендации

В следующих разделах описаны рекомендации по использованию пакета SDK.

### <a name="pull-configuration-properties-into-the-application"></a>Извлечение свойств конфигурации в приложение

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

Обратите внимание, что этот класс содержит элемент, соответствующий каждому свойству конфигурации **application.properties**, и что для каждого элемента `CosmosProperties` предоставляет методы `get` и `set`. Заметка `@ConfigurationProperties` определяет класс как представляющий свойства конфигурации, а аргумент `prefix = "cosmos"` указывает, что определенный *элемент* `CosmosProperties` сопоставляется со свойством `cosmos.member` в **application.properties**.

В следующем разделе показано, как внедрить класс `CosmosProperties` в автоматизированный поток настройки. Во время настройки создается экземпляр `CosmosProperties`, а его методы заполняются параметрами конфигурации в **application.properties**. Этот экземпляр свойств позволяет приложению считывать и изменять свойства конфигурации во время выполнения.

### <a name="configure-the-application-based-on-properties"></a>Настройка приложения на основе свойств

Следующий этап — создание класса конфигурации, который автоматизирует настройку приложения, как показано ниже:

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

Давайте рассмотрим, как создается предыдущий пример. Чтобы создать структуру класса конфигурации, сделайте следующее:

1. Расширьте класс `AbstractCosmosConfiguration`, чтобы настроить конфигурацию приложения (ключ Azure Cosmos DB, URL-адрес, имя базы данных и т. д.).
1. Добавьте заметку `@Configuration`.
1. В зависимости от используемого репозитория добавьте одну или обе заметки `@EnableCosmosRepositories` и `@EnableReactiveCosmosRepositories`.
1. Добавьте заметку `@PropertySource("classpath:application.properties")`, сигнализирующую об извлечении пары "ключ-значение" свойств из **application.properties**.
1. Добавьте заметку `@EnableConfigurationProperties`, указывающую Spring Data на класс, который может хранить пары "ключ-значение", из **application.properties**. Эта заметка принимает определение класса в качестве аргумента, поэтому передайте `CosmosProperties.class`.

Класс конфигурации использует следующие элементы:

* Объявите и определите элемент `logger` log4j2, который Spring Data будет использовать для всех выходных данных журналов.
* Объявите элемент `@Autowired` `CosmosProperties`, в который будут помещены параметры **application.properties**.

Функция `AzureKeyCredential` позволяет динамически менять ключи. Чтобы включить эту функцию, определите элемент `AzureKeyCredential`. Ключи можно переключить, добавив метод `switchToSecondaryKey`, как показано в примере выше.

Далее необходимо определить, как должна выполняться автоматическая настройка.
1. Определите метод `@Bean` `cosmosClientBuilder` для управления инициализацией клиента с помощью `CosmosClientBuilder`. Этот метод предназначен для выполнения базовой настройки клиента (т. е. для указания URI конечной точки учетной записи и ключа доступа). URI конечной точки учетной записи и ключ доступа обычно определяются в файле **application.properties**, значения из которого также переносятся в `properties`. 
1. Элемент `azureKeyCredential` можно инициализировать с помощью `properties.getKey()`. 
1. Затем можно передать `properties.getUri()` в метод построителя `endpoint` и `this.azureKeyCredential` в метод построителя `key`. 

Обратите внимание, что в предыдущем примере `cosmosClientBuilder` не вызывает `build()` в построителе клиентов. Возвращается структура незавершенного построителя. Spring Data позволяет выполнять настройку в два этапа: сначала `cosmosClientBuilder` применяет базовую конфигурацию и возвращает структуру конфигурации, а затем Spring Data вызывает метод `cosmosConfig`, который позволяет определить более сложную конфигурацию, например метрики и параметры диагностики. 

Далее мы рассмотрим эту расширенную конфигурацию в методе `cosmosConfig`:
1. Создайте метод `@Bean` `cosmosConfig`, как показано выше.
   
   Azure Cosmos DB может возвращать данные диагностики на стороне сервера, связанные с каждым запросом. Spring Data позволяет преобразовывать необработанные выходные данные диагностики перед их регистрацией в журнале, определяя обработчик данных диагностики клиента. 

1. Кроме того, как обсуждалось ранее, определите класс, реализующий `ResponseDiagnosticsProcessor` и переопределяющий метод `processResponseDiagnostics`. Вы можете определить `processResponseDiagnostics` для управления тем, как обрабатываются выходные данные диагностики. В приведенном выше примере необработанные данные диагностики просто записываются в журнал.

1. Чтобы включить диагностику и инициализировать обработчик данных диагностики, вызовите метод построителя `responseDiagnosticsProcessor`, который передает новый экземпляр класса обработчика клиента, как показано ниже:

    ```java
    return CosmosConfig.builder()
                       .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
    ```

   Azure Cosmos DB также имеет специализированные функции метрик производительности для запросов, которые называются метриками запросов. Как показано выше, в **application.properties** рекомендуется использовать параметр, отвечающий за включение и отключение метрик запросов. 
   
1. Примените этот параметр конфигурации, добавив метод построителя `.enableQueryMetrics(properties.isQueryMetricsEnabled())` в `cosmosConfig`.

1. Мы рекомендуем использовать прямое подключение для обеспечения минимальной задержки и максимальной пропускной способности, поэтому вы также можете настроить его в построителе клиента.

1. После расширенной настройки в `cosmosConfig` активируйте создание клиента. Для этого вызовите `build()` в структуре конфигурации. Будет создан экземпляр клиента Azure Cosmos DB на основе параметров конфигурации.

1. Последним этапом в определении процесса настройки будет добавление метода `@Override` `getDatabaseName()`, который возвращает имя базы данных Azure Cosmos DB в виде строки.

### <a name="customize-the-configuration"></a>Настройка конфигурации

Кроме того, вы можете настроить конфигурацию, чтобы изменить режим подключения, максимальный размер пула подключений, время ожидания запроса и т. д., как показано в следующем примере:

```java
    @Bean
    public CosmosConfig cosmosConfig() {

        // Set the connection mode to Direct (TCP), which applies to data plane operations.
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig(); 

        // Even in Direct mode, some control plane operations always pass through the gateway as HTTP requests (that is, container/database CRUD [create, retrieve, update, and delete]).
        // Optionally, you can customize connection properties for these specific operations, which are always Gateway mode.
        GatewayConnectionConfig gatewayConnectionConfig = GatewayConnectionConfig.getDefaultConfig(); 

        // Set the maximum number of HTTP connections to 1000 per application.
        gatewayConnectionConfig.setMaxConnectionPoolSize(1000);

        // Set the request timeout to 10 seconds.
        gatewayConnectionConfig.setIdleConnectionTimeout(Duration.ofMillis(10000));

        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig, gatewayConnectionConfig); // directMode() has an override that accepts Gateway config.                        
                           .build();
    }
```

### <a name="response-diagnostics-and-query-metrics"></a>Диагностика ответов и метрики запросов

Версия 2 пакета SDK для Spring Data Azure Cosmos DB поддерживает строку диагностики ответов и метрики запросов.

Чтобы включить метрики запросов, в файле `application.properties` установите для флага `queryMetricsEnabled` значение **true**. 

Затем выполните инструкции из предыдущего раздела, чтобы расширить интерфейс `ResponseDiagnosticsProcessor` и реализовать метод `processResponseDiagnostics` для записи диагностических сведений в журнал. 

Наконец, передайте экземпляр реализации в метод `CosmosDbConfig.setResponseDiagnosticsProcessor`.

### <a name="pagination-and-sorting"></a>Разбиение на страницы и сортировка

Пакет SDK для Spring Data Azure Cosmos DB поддерживает разбиение на страницы и сортировку данных Spring Data. Дополнительные сведения см. в разделе Special parameter handling (Специальная обработка параметров) [справочной документации по Spring Data](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters).

В зависимости от единиц запроса (ЕЗ), доступных для учетной записи базы данных, Azure Cosmos DB может возвращать документы, размер которых меньше или равен запрошенному размеру. Дополнительные сведения см. в статье [Единицы запроса в базе данных Azure Cosmos DB](/azure/cosmos-db/request-units).

Не следует полагаться на значение `totalPageSize`, так как число возвращаемых документов в каждой итерации является переменным. Вместо этого необходимо выполнить итерацию по объекту `Pageable`, как показано в следующем примере:

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

В следующих разделах описаны проблемы, о которых следует знать при использовании пакета SDK для Spring Data Azure Cosmos DB.

### <a name="get-the-correct-azure-cosmos-db-configuration"></a>Получение правильной конфигурации Azure Cosmos DB

Расширение интерфейса `AbstractCosmosConfiguration` может быть непростой задачей из-за разнообразных заметок и конфигураций, присутствующих в классе. Чаще всего проблемы связаны с заметкой `Enable Repositories`.

Если репозитории расширяют `CosmosRepository`, добавьте заметку `@EnableCosmosRepositories`. Если репозитории расширяют `ReactiveCosmosRepository`, добавьте заметку `@EnableReactiveCosmosRepositories`. Использование этих заметок показано в следующем примере:

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

`AzureKeyCredential` должен быть singleton-объектом, так как пакет SDK для Azure Cosmos DB использует один и тот же объект для обнаружения изменений значения ключа внутри этого объекта.

### <a name="custom-query-execution"></a>Выполнение настраиваемых запросов

Пакет SDK Spring Data Azure Cosmos DB версии 3.x.x позволяет использовать заметку `@query` для определения пользовательских запросов.

Простой пример определения запросов offset и limit с помощью заметки `@query` показан в следующем коде:

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {

    ...

    @Query(value = "SELECT * from c OFFSET @skipCount LIMIT @takeCount")
    List<SampleEntity> findByName(@Param("skipCount") int skipCount, @Param("takeCount") int takeCount);
}
```

### <a name="enable-diagnostics-and-query-metrics"></a>Включение диагностики и метрик запросов

При отладке удобно использовать строку диагностики ответов и метрики запросов из пакета SDK для Azure Cosmos DB. Пакет SDK для Azure Cosmos DB записывает строку диагностики ответов на стороне клиента. Серверная часть записывает данные метрик запросов и предоставляет их пакету SDK для Azure Cosmos DB.

Метод `ResponseDiagnosticsProcessor.processResponseDiagnostics` вызывается после каждого вызова API в пакете SDK для Spring Data Azure Cosmos DB. Поэтому важно, чтобы ваша реализация обеспечивала высокую производительность за счет отсутствия ошибок и простоты. Например, не следует записывать в журнал полный набор диагностических сведений в этом методе, так как этот объем информации значительно повлияет на производительность. Кроме того, следует использовать уровень ведения журнала `Debug`, чтобы не влиять на производительность приложения.

Пример реализации интерфейса `ResponseDiagnosticsProcessor` показан в следующем коде:

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log the Azure Cosmos DB response diagnostics:
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

## <a name="troubleshoot-common-issues"></a>Устранение распространенных неполадок

В следующих разделах описаны способы устранения распространенных проблем.

### <a name="connection-issues"></a>Проблемы с подключением

При возникновении проблем с подключением убедитесь, что в классе конфигурации указаны все правильные обязательные заметки, как описано в разделе [Получение правильной конфигурации Azure Cosmos DB](#get-the-correct-azure-cosmos-db-configuration).

### <a name="naming-changes"></a>Изменение имен.

В пакете SDK для Spring Data Azure Cosmos DB версии 3.1.0 и выше реализованы следующие важные изменения в именах и интерфейсах классов, методов, заметок и артефактов Maven:
* Идентификатор группы изменен на `com.azure`.
* Идентификатор артефакта изменен на `azure-spring-data-cosmos`.
* Типы возвращаемых значений API синхронизации изменены на типы `Iterable` (вместо `List`).
* Изменено с `CosmosDbFactory` на `CosmosFactory`.
* Изменено с `CosmosDBConfig` на `CosmosConfig`.
* Изменено с `CosmosDBAccessException` на `CosmosAccessException`.
* Заметка `Document` изменена на заметку `Container`.
* Заметка `DocumentIndexingPolicy` изменена на заметку `CosmosIndexingPolicy`.
* Изменено с `DocumentQuery` на `CosmosQuery`.
* Флаг **application.properties** `populateQueryMetrics` изменен на `queryMetricsEnabled`.

### <a name="key-bug-fixes"></a>Основные исправления ошибок

В пакете SDK Spring Data Azure Cosmos DB версии 3.1.0 и выше реализованы следующие основные исправления ошибок:
* Исправлена проблема, из-за которой запросы с заметками не принимали имя контейнера с заметкой.
* Планирование задачи ведения журнала диагностики теперь выполняется с параллельными потоками. Это предотвращает блокировку потоков ввода-вывода Netty.
* Исправлена проблема с оптимистической блокировкой для операции удаления.
* Исправлена проблема с экранированием запросов для предложения IN.
* Исправлена проблема с разрешением типа данных long для @Id.
* Исправлена проблема с разрешением типов данных boolean, long, int и double для заметки @PartitionKey.
* Исправлены ключевые слова IgnoreCase и AllIgnoreCase для игнорирования запросов регистра.
* Удалено значение единицы запроса по умолчанию (4000) при автоматическом создании контейнеров.
* Исправлена ошибка вложенного ключа секции при использовании с заметкой @GeneratedValue.

### <a name="api-or-query-slowness"></a>Медленная работа API или запросов

Если у вас возникают большие задержки в работе API-интерфейсов или выполнении запросов, попробуйте вести журнал строк диагностики и метрик запросов, как описано в разделе [Включение диагностики и метрик запросов](#enable-diagnostics-and-query-metrics). Проверьте данные об использовании ЦП, пропускной способности сети и дискового пространства ввода-вывода, которые могут являться первопричиной медленной работы на стороне клиента.
