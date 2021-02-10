---
title: Клиенты и конвейеры HTTP в пакете Azure SDK для Java
description: Общие сведения о функциональных возможностях клиентов и конвейеров HTTP в пакете Azure SDK для Java
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 93595096bc6d9fde1226f6875b866ea28b0a85bb
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528686"
---
# <a name="http-clients-and-pipelines-in-the-azure-sdk-for-java"></a>Клиенты и конвейеры HTTP в пакете Azure SDK для Java

В этой статье приводятся общие сведения об использовании функциональных возможностей клиентов и конвейеров HTTP в пакете Azure SDK для Java. Они обеспечивают согласованное, эффективное и гибкое взаимодействие с системой для разработчиков, которые пользуются всеми библиотеками Azure SDK для Java.

## <a name="http-clients"></a>HTTP-клиенты

Пакет Azure SDK для Java реализуется на основе абстракции `HttpClient`. Эта абстракция позволяет создать модульную архитектуру с подключением множества клиентских библиотек HTTP или пользовательских реализаций. Но все клиентские библиотеки Azure имеют зависимость от `azure-core-http-netty`, что для большинства пользователей позволяет упростить управление зависимостями. Соответственно, HTTP-клиент [Netty](https://netty.io) используется по умолчанию во всех библиотеках Azure SDK для Java.

Несмотря на использование Netty в качестве HTTP-клиента по умолчанию, пакет SDK предоставляет три реализации клиента, выбор между которыми определяется другими зависимостями проекта. К ним относятся:

* [Netty](https://netty.io);
* [OkHttp](https://square.github.io/okhttp/);
* новый клиент [HttpClient](https://openjdk.java.net/groups/net/httpclient/intro.html), появившийся в JDK версии 11.

### <a name="replace-the-default-http-client"></a>Замена HTTP-клиента по умолчанию

Если вы предпочитаете другую реализацию, вы можете удалить зависимость от Netty из файлов конфигурации сборки. В файле Maven *pom.xml* нужно убрать зависимость от Netty и добавить другую зависимость.

В следующем примере показано, как исключить зависимость от Netty из реальной зависимости от библиотеки `azure-security-keyvault-secrets`. Не забудьте исключить Netty из всех соответствующих библиотек `com.azure`, как показано ниже:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-security-keyvault-secrets</artifactId>
    <version>4.2.2.</version>
    <exclusions>
      <exclusion>
        <groupId>com.azure</groupId>
        <artifactId>azure-core-http-netty</artifactId>
      </exclusion>
    </exclusions>
</dependency>

<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-http-okhttp</artifactId>
  <version>1.3.3</version>
</dependency>
```

> [!NOTE]
> Если вы удалите зависимость от Netty, не предоставив взамен другую реализацию, приложение не сможет запуститься. В пути классов должна существовать реализация `HttpClient`.

### <a name="configure-http-clients"></a>Настройка HTTP-клиентов

При сборке клиента службы по умолчанию будет использоваться `HttpClient.createDefault()`. Этот метод возвращает базовый экземпляр `HttpClient` на основе предоставленной реализации HTTP-клиента. Если вам нужен более сложный HTTP-клиент, например прокси-сервер, вы можете создать специально настроенный экземпляр `HttpClient` с помощью построителя, который предоставляется в каждой реализации. Это построители `NettyAsyncHttpClientBuilder`, `OkHttpAsyncHttpClientBuilder` и `JdkAsyncHttpClientBuilder`.

В следующих примерах показано, как скомпилировать экземпляры `HttpClient` с применением Netty, OkHttp и HTTP-клиента из JDK 11. Эти экземпляры используют `http://localhost:3128` в качестве прокси-сервера и выполняют аутентификацию с именем пользователя *example* и паролем *weakPassword*.

```java
// Netty
HttpClient httpClient = new NettyAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// OkHttp
HttpClient httpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// JDK 11 HttpClient
HttpClient client = new JdkAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();
```

После этого созданный экземпляр `HttpClient` можно передать в построитель клиента службы, чтобы использовать в качестве клиента для взаимодействия с этой службой. В следующем примере новый экземпляр `HttpClient` применяется для компиляции клиента Azure Storage Blob.

```java
BlobClient blobClient = new BlobClientBuilder()
    .connectionString(<connection string>)
    .containerName("container")
    .blobName("blob")
    .httpClient(httpClient)
    .build();
```

Для библиотек управления значение `HttpClient` можно задать во время настройки диспетчера.

```java
AzureResourceManager azureResourceManager = AzureResourceManager.configure()
    .withHttpClient(httpClient)
    .authenticate(credential, profile)
    .withDefaultSubscription();
```

## <a name="http-pipeline"></a>Конвейер HTTP

Конвейер HTTP является одним из ключевых компонентов для достижения согласованности и поддержки диагностики в клиентских библиотеках Java для Azure. Конвейер HTTP состоит из следующих элементов:

* транспорт HTTP;
* политики конвейера HTTP.

При создании клиента вы можете указать собственный настраиваемый конвейер HTTP. Если вы не предоставите свой конвейер, клиентская библиотека создаст конвейер, заранее настроенный для работы с этой клиентской библиотекой.

### <a name="http-transport"></a>Транспорт HTTP

Транспорт HTTP отвечает за установление подключения к серверу, за отправку и получение HTTP-сообщений. Транспорт HTTP формирует шлюз для клиентских библиотек пакета Azure SDK, через который они взаимодействуют со службами Azure. Как отмечалось ранее в этой статье, пакет Azure SDK для Java использует [Netty](https://netty.io/) в качестве транспорта HTTP по умолчанию. Но этот же пакет SDK предоставляет и отдельный подключаемый транспорт HTTP, что позволяет по мере необходимости использовать другие реализации. Также пакет SDK предоставляет две дополнительные реализации транспорта HTTP для OkHttp и HTTP-клиента, который входит в состав JDK 11 и более поздних версий.

### <a name="http-pipeline-policies"></a>Политики конвейера HTTP

Конвейер HTTP состоит из последовательности шагов, которые выполняются для каждой пары "запрос — ответ". Каждая политика имеет определенное назначение и применяется к запросу, к ответу или к обоим сразу. Все клиентские библиотеки включают стандартный уровень "Azure Core", что гарантирует правильный порядок выполнения политик в конвейере. При обработке запроса политики выполняются в том порядке, в котором были добавлены в конвейер. При обработке ответа от службы те же политики выполняются в обратном порядке. Все добавленные в конвейер политики выполняются перед отправкой запроса и после получения ответа. Политика должна сама решить, будет ли она выполнять какие-то действия для запроса, для ответа или для обоих. Например, политика ведения журнала записывает в журнал запрос и ответ, но политика аутентификации учитывает только изменение запроса.

Платформа Azure Core предоставит политике необходимые данные о запросе и ответе, а также важный контекст для выполнения этой политики. На их основе политика может выполнить требуемые действия с данными и передать управление следующей политике в конвейере.

![Схема конвейера HTTP](./media/http-pipeline.svg)

### <a name="http-pipeline-policy-position"></a>Расположение политики в конвейере HTTP

При выполнении HTTP-запросов к облачным службам важно правильно обрабатывать временные сбои и выполнять повторные попытки. Поскольку эти возможности требуются во многих случаях, Azure Core предоставляет стандартную политику повторных попыток, которая умеет отслеживать временные сбои и автоматически повторять запрос.

Таким образом политика повторных попыток разделяет весь конвейер на две части: политики, которые выполняются до политики повтора, и политики, которые выполняются после нее. Политики, добавляемые перед политикой повтора, выполняются только один раз для каждой операции API, а политики, добавленные после политики повтора, выполняются столько же раз, сколько будет выполнено попыток.

Это означает, что при создании конвейера HTTP важно хорошо понимать, какие политики нужно применять для каждого повтора запроса, а какие — один раз для операции API.

### <a name="common-http-pipeline-policies"></a>Типичные политики в конвейере HTTP

Конвейеры HTTP для служб на основе интерфейсов REST имеют в своей конфигурации политики для аутентификации, повторных попыток, ведения журнала, телеметрии и указания идентификатора запроса в заголовке. Azure Core включает стандартные реализации популярных политик HTTP, которые можно добавить в конвейер.

| Политика                | Ссылка на GitHub        |
|-----------------------|--------------------|
| Политика повтора          | [RetryPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RetryPolicy.java) |
| Политика проверки подлинности | [BearerTokenAuthenticationPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/BearerTokenAuthenticationPolicy.java) |
| Политика ведения журнала        | [HttpLoggingPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/HttpLoggingPolicy.java) |
| Политика идентификатора запроса     | [RequestIdPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RequestIdPolicy.java) |
| Политика телеметрии      | [UserAgentPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/UserAgentPolicy.java) |

### <a name="custom-http-pipeline-policy"></a>Настраиваемая политика в конвейере HTTP

Политика конвейера HTTP представляет собой удобный механизм для изменения или дополнения запросов и ответов. К конвейеру можно добавить собственные настраиваемые политики, которые создаются пользователями и разработчиками клиентских библиотек. При добавлении политики в конвейер вы можете указать, нужно ли выполнять эту политику для каждого вызова или для каждой попытки.

Чтобы создать настраиваемую политику конвейера HTTP, достаточно расширить базовый тип политики и реализовать некоторый абстрактный метод. Готовую политику можно подключить к конвейеру.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы ознакомились с функциональными возможностями HTTP-клиента в пакете Azure SDK для Java, переходите к статье [Настройка прокси-серверов в пакете Azure SDK для Java](proxying.md), где описаны возможности по настройке используемых HTTP-клиентов.
