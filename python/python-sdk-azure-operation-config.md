---
title: Пакет Azure SDK для конфигурации операций Python
description: C, вызываемый пакетом Azure SDK для Python
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.openlocfilehash: 9638aa4602f96e2da0155a7b3840e5be4857eb98
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68285515"
---
# <a name="operation-config"></a>Конфигурация операций 

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
