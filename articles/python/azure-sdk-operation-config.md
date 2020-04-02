---
title: Параметры конфигурации операций. Azure SDK для Python
description: C, вызываемый пакетом Azure SDK для Python
ms.date: 03/07/2018
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: b0aaaf5bcd51bce42ab38830d5dbce508db226b1
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441699"
---
# <a name="parameters-for-operation-configuration"></a>Параметры конфигурации операций

Вы можете указать дополнительные параметры для методов в операциях в пакете Azure SDK для Python.

Дополнительные параметры доступны в `kwargs`. Эта функция называется *operation_config*.

Параметры конфигурации приложения:

|Имя параметра|Тип|Роль|
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
