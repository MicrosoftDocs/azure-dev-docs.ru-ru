---
title: Параметры конфигурации операций. Azure SDK для Python
description: C, вызываемый пакетом Azure SDK для Python
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: ca69b72789f28445c4654e635e641e2954890a38
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172372"
---
# <a name="parameters-for-operation-configuration"></a>Параметры конфигурации операций

Методы, используемые с операциями, имеют дополнительные параметры, которые могут быть предоставлены в `kwargs`. Это называется operation_config.

Параметры конфигурации приложения:

|Имя параметра|type|Роль|
|----------------------|------|---------------|
| проверка |`bool`|Проверка SSL-сертификата. Значение по умолчанию — True.|
|  cert |`str`| Путь к локальному сертификату для проверки на стороне клиента.|
|  timeout |`int`| Время ожидания для установки подключения к серверу в секундах.|
|  allow_redirects |`bool` | Разрешение перенаправлений.|
|  allow_redirects  |`int`| Максимальное допустимое число перенаправлений.|
|  proxies  |`dict` |Параметры прокси-сервера.|
|  use_env_proxies |`bool` |Чтение параметров прокси-сервера из локальных переменных среды.|
|  retries  |`int` | Общее число повторных попыток.|
|  enable_http_logger | `bool`| Включение журналов HTTP-протокола в режиме отладки (по умолчанию — False).|
