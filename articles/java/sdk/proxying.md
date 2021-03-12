---
title: Настройка прокси-серверов в пакете SDK Azure для Java
description: Обзор концепций пакета SDK Azure для Java, связанных с использованием прокси-сервера
author: alzimmermsft
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: alzimmer
ms.openlocfilehash: 03acb768d348505b03419eb132c1d9623633b442
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118172"
---
# <a name="configure-proxies-in-the-azure-sdk-for-java"></a>Настройка прокси-серверов в пакете SDK Azure для Java

В этой статье приведены общие сведения о настройке пакета SDK Azure для Java для правильного использования прокси-серверов.

## <a name="http-proxy-configuration"></a>Конфигурация прокси-сервера HTTP

Клиентские библиотеки Azure для Java предлагают несколько способов настройки прокси-сервера для `HttpClient`.

Каждый метод предоставления прокси-сервера имеет свои достоинства и недостатки и обеспечивает различные уровни инкапсуляции. После настройки прокси-сервера для `HttpClient` он будет использоваться в течение всего времени существования. Если прокси-сервер привязан к отдельному `HttpClient`, это позволит приложению использовать несколько экземпляров `HttpClient`. Каждый экземпляр сможет использовать другой прокси-сервер, чтобы удовлетворить требования к перенаправлению приложения.

Параметры конфигурации прокси-сервера:

* [Использование прокси-сервера среды](#use-an-environment-proxy)
* [Использование прокси-сервера конфигурации](#use-a-configuration-proxy)
* [Использование явного прокси-сервера](#use-an-explicit-proxy)

### <a name="use-an-environment-proxy"></a>Использование прокси-сервера среды

По умолчанию построители клиентов HTTP будут проверять среду на наличие конфигураций прокси-сервера. В этом процессе используются API-интерфейсы `Configuration` пакета SDK Azure для Java. Когда построитель создает клиент, он настраивается с помощью копии "глобальной конфигурации", полученной путем вызова `Configuration.getGlobalConfiguration()`. Этот вызов будет считываться в любой конфигурации прокси-сервера HTTP из среды системы.

Когда построитель проверяет среду, он будет искать следующие конфигурации среды в указанном порядке:

1. `HTTPS_PROXY`
2. `HTTP_PROXY`
3. `https.proxy*`
4. `http.proxy*`

`*` представляет хорошо известные свойства прокси-сервера Java. См. сведения о [сетевом взаимодействии и прокси-серверах Java](https://docs.oracle.com/javase/8/docs/technotes/guides/net/proxies.html) в документации по Oracle.

Если построитель обнаруживает любую из конфигураций среды, он создает экземпляр `ProxyOptions` путем вызова `ProxyOptions.fromConfiguration(Configuration.getGlobalConfiguration())`. В этой статье приведены дополнительные сведения о типе `ProxyOptions`.

> [!Important]
> Для использования любой конфигурации прокси-сервера в Java необходимо задать для свойства среды системы `java.net.useSystemProxies` значение `true`.

Вы также можете создать экземпляр клиента HTTP, который не использует конфигурацию прокси-сервера в переменных среды системы. Чтобы переопределить поведение по умолчанию, в построителе клиента HTTP необходимо явным образом задать по-разному настроенную `Configuration`. При установке `Configuration` в построителе больше не будет вызываться метод `Configuration.getGlobalConfiguration()`. Например, при вызове `configuration(Configuration)` с помощью `Configuration.NONE` можно явно запретить построителю проверять среду для настройки.

В следующем примере используется переменная среды `HTTP_PROXY` со значением `localhost:8888`, чтобы использовать Fiddler в качестве прокси-сервера. В следующем примере кода показано создание HTTP-клиента Netty и OkHttp. (Дополнительные сведения о конфигурации клиента HTTP см. в статье [Клиенты и конвейеры HTTP](http-client-pipeline.md).)

```bash
export HTTP_PROXY=localhost:8888
```

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder().build();
HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder().build();
```

Чтобы предотвратить использование прокси-сервера среды, настройте построитель клиентов HTTP с помощью `Configuration.NONE`, как показано в следующем примере:

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();
```

### <a name="use-a-configuration-proxy"></a>Использование прокси-сервера конфигурации

Чтобы не считывать данные из среды, можно настроить построители клиентов HTTP для использования настраиваемого `Configuration` с теми же параметрами прокси-сервера, которые уже приняты из среды. Эта конфигурация предоставляет возможность использования многократно применяемых конфигураций с ограниченным использованием. Когда построитель клиента HTTP создает `HttpClient`, он будет использовать `ProxyOptions`, возвращенный из `ProxyOptions.fromConfiguration(<Configuration passed into the builder>)`.

В следующем примере иллюстрируется применение конфигураций `http.proxy*`, заданных в объекте `Configuration`, для использования прокси-сервера, который проверяет подлинность Fiddler в качестве прокси-сервера.

```java
Configuration configuration = new Configuration()
    .put("java.net.useSystemProxies", "true")
    .put("http.proxyHost", "localhost")
    .put("http.proxyPort", "8888")
    .put("http.proxyUser", "1")
    .put("http.proxyPassword", "1");

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();
```

### <a name="use-an-explicit-proxy"></a>Использование явного прокси-сервера

Клиентские библиотеки Java поставляются с классом `ProxyOptions`, который выступает в качестве типа клиентских библиотек Azure для настройки прокси-сервера. `ProxyOptions` можно настроить с помощью сетевого протокола, используемого для отправки запросов прокси-сервера, его адреса, учетных данных проверки подлинности и узлов, не использующих прокси-сервер. Требуются только сетевой протокол и адрес прокси-сервера. При использовании учетных данных для проверки подлинности необходимо задать имя пользователя и пароль.

В следующем примере создается простой экземпляр `ProxyOptions`, который передает запросы по стандартному адресу Fiddler (`localhost:8888`).

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888));
```

В следующем примере создается экземпляр `ProxyOptions` с проверкой подлинности, который передает запросы в экземпляр Fiddler, для которого требуется проверка подлинности прокси-сервера.

```java
// Fiddler uses username "1" and password "1" with basic authentication as its proxy authentication requirement.
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888))
    .setCredentials("1", "1");
```

Вы можете настроить построители клиентов HTTP с `ProxyOptions` напрямую, чтобы указать явный прокси-сервер для использования. Эта конфигурация является наилучшим способом предоставления прокси-сервера. Она, как правило, не такая гибкая, как передача `Configuration`, которую можно изменить, чтобы обновить требования к прокси-серверу.

В следующем примере применяются `ProxyOptions` для использования Fiddler в качестве прокси-сервера.

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 8888));

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знакомы с конфигурацией прокси-сервера в пакете SDK Azure для Java, ознакомьтесь со статьей [Настройка трассировки в Azure SDK для Java](tracing.md), чтобы получить сведения о потоках в пределах приложения и найти инструкции для диагностики проблем.
