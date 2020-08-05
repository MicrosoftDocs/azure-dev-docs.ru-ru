---
title: Ведение журналов с помощью пакета Azure SDK для JavaScript
description: Узнайте, как включить ведение журналов с помощью клиентских библиотек пакета Azure SDK для JavaScript.
ms.topic: article
ms.date: 07/23/2020
ms.author: dsindona
author: dsindona
ms.openlocfilehash: 1fd961709723891824cedfd0d9b853a328cb2e46
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335281"
---
# <a name="logging-with-the-azure-sdk-for-javascript"></a>Ведение журналов с помощью пакета Azure SDK для JavaScript

Основная библиотека пакета Azure SDK для JavaScript включает библиотеку средства ведения журналов, которая позволяет с легкостью включить ведение журналов для приложения. 

[Пакет NPM](https://www.npmjs.com/package/@azure/logger) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)

## <a name="enable-logging"></a>Включение ведения журналов

Вы можете включить ведение журнала для всех компонентов приложения с помощью одной переменной среды или же задать динамическое ведение журнала для одного компонента. В этой статье представлены основные сведения о пакете средства ведения журналов и включении ведения журналов с помощью следующих методов:

- Установите переменную среды `AZURE_LOG_LEVEL`.
- вызов функции `setLogLevel`, импортированной из библиотеки ведения журналов;
- вызов функции `enable()` для конкретных средств ведения журналов.

> [!NOTE]
> Эта статья применима к клиентским библиотекам, которые используют последние версии пакета Azure SDK. Чтобы узнать, поддерживается ли библиотека, обратитесь к списку [последних выпусков Azure SDK](https://azure.github.io/azure-sdk/releases/latest/index.html#javascript). Если приложение использует более раннюю версию клиентских библиотек пакета Azure SDK, ознакомьтесь с конкретными инструкциями в соответствующей документации службы.

## <a name="log-levels"></a>Уровни журнала

Пакет `@azure/logger` поддерживает следующие уровни журналов, указанные, начиная от самых подробных:

- verbose
- сведения
- warning
- error

Если вы зададите уровень журнала программными средствами или с помощью переменной среды `AZURE_LOG_LEVEL`, будут выданы все журналы с указанным или более низким уровнем. Например, если задать уровень журнала `warning`, будут выданы все журналы с уровнем `warning` или `error`.

## <a name="install-the-logger-package"></a>Установка пакета ведения журналов

Библиотека ведения журналов из пакета Azure SDK для JavaScript предоставляется в виде пакета [npm](https://www.npmjs.com/). Используйте npm, чтобы установить пакет `@azure/logger`.

```cmd
npm install @azure/logger
```

## <a name="set-the-logging-environment-variable"></a>Настройка переменной среды для ведения журналов

Вы можете использовать одну переменную среды `AZURE_LOG_LEVEL`, чтобы включить ведение журналов в приложении. Журналы будут выводиться в stderr. После задания переменной среды, чтобы начать создание журналов необходимо перезапустить приложение.

Этот пример Bash задает подробное ведение журналов:

```bash
export AZURE_LOG_LEVEL="verbose"
```

В этом примере используется PowerShell:

```powershell
$env:AZURE_LOG_LEVEL="verbose"
```

В этом примере используется CMD:

```cmd
set AZURE_LOG_LEVEL="verbose"
```

## <a name="configure-dynamic-logging"></a>Настройка динамического ведения журналов

Пакет Azure SDK для JavaScript позволяет динамически включать ведение журналов по необходимости или для конкретных клиентских библиотек. Этот вариант подойдет разработчикам, которые хотят использовать собственную реализацию ведения журналов для некоторых компонентов приложений или временно включить журналы для отладки.

С помощью модуля  `@azure/logger`  вы можете задать уровень журнала в коде.

```js
import { setLogLevel } from "@azure/logger";

setLogLevel("verbose");
```

Чтобы включить конкретный канал журнала, импортируйте `logger` из пакета, для которого вы хотите выдавать журналы. В следующем примере ведение журнала info включается только для Центров событий.

```js
import { logger } from "@azure/event-hubs";

logger.info.enable();
```

## <a name="redirect-log-output"></a>Перенаправление выходных данных журналов

Чтобы самостоятельно обрабатывать сообщения журнала, переназначьте метод `log` для `AzureLogger` или любого другого средства ведения журналов, импортированного из клиентской библиотеки.

В этом примере сообщения журнала перенаправляются в stderr.

```js
import { AzureLogger } from "@azure/logger";

AzureLogger.log = msg => console.error(msg);
```

В этом примере перенаправляются только сообщения журнала info из концентратора событий Azure.

```js
import { logger } from "@azure/event-hubs";
logger.info.log = msg => console.error(msg);
```

## <a name="next-steps"></a>Следующие шаги

- [Включение функции ведения журналов диагностики для приложений в Службе приложений Azure](/azure/app-service/troubleshoot-diagnostic-logs)
- [Просмотрите параметры ведения журнала безопасности и аудита в Azure](/azure/security/fundamentals/log-audit)
- [Узнайте о том, как работать с журналами платформы Azure](/azure/azure-monitor/platform/platform-logs-overview)