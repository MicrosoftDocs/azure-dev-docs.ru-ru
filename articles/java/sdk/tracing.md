---
title: Настройка трассировки в Azure SDK для Java
description: Обзор концепций Azure SDK для Java, связанных с трассировкой.
author: samvaity
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: savaity
ms.openlocfilehash: e5ead01c22fdb80e7c5fc49c3a980add883eb6d9
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395219"
---
# <a name="configure-tracing-in-the-azure-sdk-for-java"></a>Настройка трассировки в Azure SDK для Java

В этой статье приведены общие сведения о настройке пакета Azure SDK для Java для внедрения возможностей трассировки.

Пакет Azure SDK для Java обеспечивает трассировку во всех клиентских библиотеках путем добавления зависимости для [подключаемого модуля azure-core-tracing-opentelemetry](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java) на основе [OpenTelemetery](https://opentelemetry.io/). OpenTelemetry — это популярная платформа наблюдаемости с открытым кодом для создания, записи и сбора данных телеметрии для ориентированного на облако программного обеспечения.

С трассировкой связаны два основных понятия: **span** и **trace**. Span представляет собой одну операцию в процессе trace. Это может быть HTTP-запрос, удаленный вызов процедуры (RPC), запрос базы данных или даже путь, который принимается кодом. Тrace — это дерево операций span, отображающее ход операций в системе. Тrace можно отличить по уникальной 16-разрядной последовательности, которая называется TraceID. Дополнительные сведения об этих понятиях и их связи с OpenTelemetry см. в [документации по OpenTelemetry](https://opentelemetry.io/docs/).

Включить трассировку в клиентских библиотеках Azure для Java можно двумя способами:

1. Включение функций, встроенных в Azure SDK для Java.
2. Включение внутрипроцессного агента для сбора данных трассировки и их отправки без изменения кода.

## <a name="enable-tracing-in-the-azure-sdk-for-java"></a>Включение трассировки в Azure SDK для Java

Чтобы включить трассировку для всех клиентских библиотек Azure для Java, добавьте зависимости `azure-core-tracing-opentelemetry` и `opentelemetry-sdk` в свое приложение. Например, в Maven добавьте следующие зависимости:

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-tracing-opentelemetry</artifactId>
  <version>1.0.0-beta.6</version>
</dependency>

<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-sdk</artifactId>
  <version>0.8.0</version>
</dependency>
```

При добавлении такой зависимости включается трассировка с добавлением процессов trace во все HTTP-запросы. Но теперь возникли две проблемы:

1. Отсутствует интеграция с какой-либо входящей родительской операцией span.
2. Созданные процессы trace не экспортируются в какое-либо расположение для последующего анализа.

Решение этих проблем рассматривается в следующих разделах.

### <a name="integrate-parent-spans"></a>Интеграция родительских операций span

Как отмечалось выше, при добавлении зависимостей включается трассировка в клиентских библиотеках Azure. Но трассировка не будет интегрирована с какими-либо входящими данными трассировки, например в веб-среде, где входящий запрос приводит к вызову клиентской библиотеки Azure. Чтобы включить трассировку, можно создать корневую операцию span в приложении и передать ее в вызовы клиентской библиотеки Azure, чтобы эту операцию span можно было инкапсулировать в соответствующие исходящие запросы к службам Azure. Эту задачу можно выполнить, используя параметр `Context` для всех методов клиента, как показано в следующем примере:

```java
// The 'span' given in this context is the parent span key received from the incoming request.
Context traceContext = new Context(PARENT_SPAN_KEY, span);

// This example code passes a new configuration setting to a service, but also includes
// the traceContext from above, so that it may be picked up by the http transport and included as appropriate.
appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world"), true, traceContext);
```

В случаях, когда родительская операция span не предоставлена, создается новая родительская операция span для инкапсуляции всех исходящих запросов клиентских библиотек. Для каждого вызова метода клиентской библиотеки Azure создаются две операции span: одна с трассировкой последовательности операций в клиентских библиотеках, а вторая с трассировкой span исходящего HTTP-запроса.

#### <a name="tracer-span-attributes"></a>Атрибуты трассировочной операции span

Помимо обязательных стандартных атрибутов, описанных в [соглашениях о семантике](https://github.com/open-telemetry/opentelemetry-specification/blob/e9340d74f1ba0b651b3581d6bd5df6a92b772e18/semantic-conventions.md)) OpenTelemetry, клиентские библиотеки Azure добавляют для операций span следующие атрибуты:

* `az.namespace`. [Пространства имен](/azure/azure-resource-manager/management/azure-services-resource-providers) поставщика ресурсов Майкрософт, сопоставленные со службами Azure.
* `x-ms-request-id`. Уникальный идентификатор запроса.
* `span.kind`. Описывает связь между операцией span, а также ее родительскими и дочерними элементами в процессе trace.
* `span.status.message`. Представляет состояние завершенной операции span.
* `span.status.code`. Представляет код состояния завершенной операции span.

Дополнительные метаданные о выполняемой операции записываются в именах операций span. Имена span-операций HTTP задаются как значение пути URI, а span вызова методов библиотеки — в виде `<namespace qualified type>.<method name>`.

Например, при запросе клиента Конфигурации приложений для установки параметра Конфигурации `appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world")` будут созданы две следующих операции span:

* span метода библиотеки с именем `AppConfig.setKey`;
* span исходящего HTTP-запроса с именем `/kv/hello`.

### <a name="configure-tracing-exports"></a>Настройка экспорта трассировки

Приложения, которым нужно использовать данные trace, должны экспортировать процессы trace в хранилище распределенной трассировки (например, [Zipkin](https://zipkin.io/), [Jaeger](https://www.jaegertracing.io/) и [Azure Monitor](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/monitor/azure-monitor-opentelemetry-exporter#azure-monitor-opentelemetry-exporter-client-library-for-java)). В следующем примере выполняется настройка экспорта данных trace в хранилище распределенной трассировки Jaeger, работающее на порте localhost 14250, с использованием API Jaeger:

```java
ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 14250).usePlaintext().build();
JaegerGrpcSpanExporter exporter = JaegerGrpcSpanExporter.newBuilder()
    .setChannel(channel)
    .setServiceName("Sample")
    .setDeadline(0)
    .build();
TracerSdkFactory tracerSdkFactory = (TracerSdkFactory) OpenTelemetry.getTracerFactory();
tracerSdkFactory.addSpanProcessor(SimpleSpansProcessor.newBuilder(exporter).build());
```

## <a name="enable-tracing-with-the-in-process-agent"></a>Включение трассировки с помощью внутрипроцессного агента

Вы можете использовать Application Insights (функция [Azure Monitor](/azure/azure-monitor/overview)) для автоматического сбора и передачи данных с целью последующего анализа приложений в крупномасштабных распределенных системах. Функция инструментирования отслеживает ваше приложение и направляет данные телеметрии в [ресурс Azure Application Insights](/azure/azure-monitor/app/app-insights-overview), используя уникальный идентификатор GUID, который называется ключом инструментирования.

Используя [внутрипроцессный агент Java](/azure/azure-monitor/app/java-in-process-agent), вы можете включить мониторинг приложений без каких-либо изменений в коде. Кроме того, вам потребуется добавить в проект зависимость [azure-core-tracing-opentelemetry](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java). После выполнения этой задачи вы сможете использовать панель мониторинга Application Insights для инструментирования запросов, сбора данных со счетчиков производительности, диагностики проблем и исключений производительности, а также создавать код для отслеживания действий пользователей в приложении.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы имеете представление об основных сквозных функциях в пакете Azure SDK для Java, ознакомьтесь со статьей [Аутентификация в Azure с помощью Java и удостоверений Azure](identity.md), чтобы узнать, как создавать защищенные приложения.
