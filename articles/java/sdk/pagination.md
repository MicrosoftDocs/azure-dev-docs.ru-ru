---
title: Разбиение на страницы и итерация в пакете Azure SDK для Java
description: Обзор понятий Azure SDK для Java, связанных с разбиением на страницы и итерацией.
author: anuchandy
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: anuchan
ms.openlocfilehash: 0cb9e519931d24dcd97aa4a52742974427df5969
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528626"
---
# <a name="pagination-and-iteration-in-the-azure-sdk-for-java"></a>Разбиение на страницы и итерация в пакете Azure SDK для Java

В этой статье приведены общие сведения о том, как использовать пакет Azure SDK для Java для эффективной и производительной работы функций разбиения на страницы и итерации с крупными наборами данных.

Многие операции, предоставляемые клиентскими библиотеками в пакете Azure SDK для Java, возвращают более одного результата. В этих случаях пакет Azure SDK для Java определяет набор допустимых типов возвращаемых данных, чтобы обеспечить максимальную согласованность для разработчиков. Используемые типы возвращаемых данных: `PagedIterable` для синхронных API-интерфейсов и `PagedFlux` для асинхронных API-интерфейсов. Между этими API-интерфейсами есть некоторые различия в аспекте вариантов использования, но по существу требования API-интерфейсов одинаковые:

- Обеспечьте возможность с легкостью выполнять итерацию по каждому отдельному элементу в коллекции, игнорируя необходимость разбиения на страницы вручную или отслеживания маркеров продолжения. `PagedIterable` и `PagedFlux` упрощают выполнение этой задачи, запуская итерацию для разбитого на страницы ответа, десериализованного в указанный тип `T`. `PagedIterable` реализует интерфейс `Iterable` и предоставляет API для получения `Stream`, а `PagedFlux` предоставляет `Flux`. Во всех случаях разбиение на страницы выполняется прозрачно, а итерация продолжается, пока не останется доступных для итерации результатов.

- Обеспечьте возможность явно выполнять постраничную итерацию. Это позволит лучше понять, когда запускаются запросы, и предоставит вам доступ к данным ответа по каждой странице. `PagedIterable` и `PagedFlux` включают методы, которые возвращают соответствующие типы для итерации по страницам, а не по отдельным элементам.

В этой статье отдельно рассмотрена работа с синхронными и асинхронными API-интерфейсами Azure SDK для Java. API-интерфейсы синхронной итерации используются с синхронными клиентами, а API -интерфейсы асинхронной итерации — с асинхронными.

## <a name="synchronous-pagination-and-iteration"></a>Синхронные разбиение на страницы и итерация

В этом разделе рассматриваются синхронные API-интерфейсы.

### <a name="iterate-over-individual-elements"></a>Итерация по отдельным элементам

Как было сказано ранее, самый популярный вариант использования представлен итерацией по каждому отдельному элементу, а не по страницам. В следующих примерах кода показано, как API `PagedIterable` позволяет использовать предпочтительный стиль итерации для реализации этой функциональности.

#### <a name="use-a-for-each-loop"></a>Использование цикла for-each

Так как `PagedIterable` реализует `Iterable`, вы можете выполнить итерацию по элементам, как показано в следующем примере:

```java
PagedIterable<Secret> secrets = client.listSecrets();
for (Secret secret : secrets) {
   System.out.println("Secret is: " + secret);
}
```

#### <a name="use-stream"></a>Использование Stream

Так как `PagedIterable` включает определенный метод `stream()`, его можно вызвать для использования стандартных API-интерфейсов Java Stream, как показано в следующем примере:

```java
client.listSecrets()
      .stream()
      .forEach(secret -> System.out.println("Secret is: " + secret));
```

#### <a name="use-iterator"></a>Использование Iterator

Так как `PagedIterable` реализует `Iterable`, он также включает метод `iterator()`, позволяющий использовать стиль программирования итератора Java, как показано в следующем примере:

```java
Iterator<Secret> secrets = client.listSecrets().iterator();
while (it.hasNext()) {
   System.out.println("Secret is: " + it.next());
}
```

### <a name="iterate-over-pages"></a>Выполнение итерации со страницами

При работе с отдельными страницами можно выполнять итерацию по каждой странице, например, если вам требуются данные HTTP-ответа или если маркеры продолжения важны для сохранения журнала итераций. Независимо от того, выполняется ли итерация по страницам или по элементам, это не оказывает никакого влияния на производительность или число вызовов к службе. Базовая реализация загружает следующую страницу по запросу, и если вы в какой-либо момент отменили подписку на `PagedFlux`, больше не выполняет вызовы к службе.

#### <a name="use-a-for-each-loop"></a>Использование цикла for-each

При вызове `listSecrets()` вы получаете `PagedIterable` с API `iterableByPage()`. Этот API выдает `Iterable<PagedResponse<Secret>>`, а не `Iterable<Secret>`. `PagedResponse` предоставляет метаданные ответа и доступ к маркеру продолжения, как показано в следующем примере:

```java
Iterable<PagedResponse<Secret>> secretPages = client.listSecrets().iterableByPage();
for (PagedResponse<Secret> page : secretPages) {
   System.out.println("Response code: " + page.getStatusCode());
   System.out.println("Continuation Token: " + page.getContinuationToken());
   page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
}
```

Также доступна перегрузка `iterableByPage`, принимающая маркер продолжения. Эту перегрузку можно вызвать, если вы позднее захотите вернуться к той же точке итерации.

#### <a name="use-stream"></a>Использование Stream

В следующем примере показано, как метод `streamByPage()` выполняет операцию, описанную выше. Кроме того, этот API также реализует перегрузку маркера продолжения для возврата в ту же точку итерации позднее.

```java
client.listSecrets()
      .streamByPage()
      .forEach(page -> {
          System.out.println("Response code: " + page.getStatusCode());
          System.out.println("Continuation Token: " + page.getContinuationToken());
          page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
      });
```

## <a name="asynchronously-observe-pages-and-individual-elements"></a>Асинхронный просмотр страниц и отдельных элементов

В этом разделе рассматриваются асинхронные API-интерфейсы. В асинхронном API сетевой вызов выполняется в потоке, отличном от основного потока, который вызывает `subscribe()`. Это означает, что основной поток может завершиться раньше, чем будет доступен результат. Вам необходимо следить за тем, чтобы приложение не завершало работу до завершения асинхронной операции.

### <a name="observe-individual-elements"></a>Просмотр отдельных элементов

В следующем примере показано, как API `PagedFlux` позволяет асинхронно просматривать отдельные элементы. Есть разные способы, с помощью которых можно подписаться на тип Flux. Дополнительные сведения см. в разделе [Простые способы, с помощью которых можно создать тип Flux или Mono и подписаться на него](https://projectreactor.io/docs/core/release/reference/#_simple_ways_to_create_a_flux_or_mono_and_subscribe_to_it) в [справочном руководстве по Reactor 3](https://projectreactor.io/docs/core/release/reference). Этот пример — всего один из возможных и включает три лямбда-выражения: по одному для объекта-получателя, объекта-получателя ошибки и объекта-получателя завершения. Наличие всех трех из них рекомендуется, но в некоторых случаях достаточно иметь только объект-получатель и, возможно, объект-получатель ошибки.

 ```java
asyncClient.listSecrets()
    .subscribe(secret -> System.out.println("Secret value: " + secret),
        ex -> System.out.println("Error listing secrets: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all secrets"));
 ```

### <a name="observe-pages"></a>Просмотр страниц

 В следующем примере показано, как API `PagedFlux` позволяет асинхронно просматривать каждую страницу, используя API `byPage()` и предоставляя объект-получатель, объект-получатель ошибки и объект-получатель завершения.

  ```java
asyncClient.listSecrets().byPage()
    .subscribe(page -> {
            System.out.println("Response code: " + page.getStatusCode());
            System.out.println("Continuation Token: " + page.getContinuationToken());
            page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
        },
        ex -> System.out.println("Error listing pages with secret: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all pages with secret"));
 ```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знакомы с разбивкой на страницы и итерацией в пакете Azure SDK для Java, изучите статью [Длительные операции в пакете Azure SDK для Java](lro.md). Длительные операции — это операции, выполняемые в течение более продолжительного времени, чем обычные HTTP-запросы, что обычно вызвано необходимостью в определенном реагировании со стороны сервера.
