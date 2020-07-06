---
title: Настройка прокси-серверов при использовании библиотек Azure
description: Используйте переменные среды HTTP[S]_PROXY, чтобы определить прокси-сервер для всего скрипта или приложения, или же используйте необязательные именованные аргументы для конструкторов клиента или методов операций.
ms.date: 06/16/2020
ms.topic: conceptual
ms.openlocfilehash: c24ad2f5cd58e6cee9b9f61cb216e949ddd446ef
ms.sourcegitcommit: b3e506c6f140d91e6fdd9dcadf22ab1aa67f6978
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947521"
---
# <a name="how-to-configure-proxies-for-the-azure-libraries"></a>Настройка прокси-серверов для библиотек Azure

URL-адрес прокси-сервера имеет вид `http[s]://[username:password@]<ip_address_or_domain>:<port>/`, где сочетание имени пользователя и пароля username:password указывать необязательно.

Вы можете затем настроить прокси-сервер глобально с помощью переменных среды или указать прокси-сервер, передав аргумент с именем `proxies` отдельному конструктору клиента или методу операции.

## <a name="global-configuration"></a>Глобальная конфигурация

Чтобы настроить прокси-сервер глобально для скрипта или приложения, определите переменные среды `HTTP_PROXY` или `HTTPS_PROXY` с URL-адресом сервера. Эти переменные работают с любой версией библиотек Azure.

Такие переменные среды игнорируются при передаче параметра `use_env_settings=False` в конструктор объектов клиента или в метод операции.

### <a name="from-python-code"></a>Из кода Python

```python
import os
os.environ["HTTP_PROXY"] = "http://10.10.1.10:1180"

# Alternate URL and variable forms:
# os.environ["HTTP_PROXY"] = "http://username:password@10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://username:password@10.10.1.10:1180"
```

### <a name="from-the-cli"></a>Из CLI

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
rem Non-authenticated HTTP server:
set HTTP_PROXY=http://10.10.1.10:1180

rem Authenticated HTTP server:
set HTTP_PROXY=http://username:password@10.10.1.10:1180

rem Non-authenticated HTTPS server:
set HTTPS_PROXY=http://10.10.1.10:1180

rem Authenticated HTTPS server:
set HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Non-authenticated HTTP server:
HTTP_PROXY=http://10.10.1.10:1180

# Authenticated HTTP server:
HTTP_PROXY=http://username:password@10.10.1.10:1180

# Non-authenticated HTTPS server:
HTTPS_PROXY=http://10.10.1.10:1180

# Authenticated HTTPS server:
HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

---

## <a name="per-client-or-per-method-configuration"></a>Настройка для отдельного клиента или метода

Чтобы настроить прокси-сервер для конкретного объекта клиента или метода операции, укажите прокси-сервер с помощью аргумента с именем `proxies`.

Например, в следующем коде из статьи [Пример. Использование библиотек Azure со службой хранилища Azure](azure-sdk-example-storage.md) указан прокси-сервер HTTPS с учетными данными пользователя с помощью конструктора `BlobClient`. В этом случае объект берется из библиотеки azure.storage.blob, которая основана на библиотеке azure.core.

```python
# Earlier code omitted for brevity

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential,
    proxies={ "https": "https://username:password@10.10.1.10:1180" }
)

# Other forms that the proxy URL might take:
# proxies={ "http": "http://10.10.1.10:1180" }
# proxies={ "http": "http://username:password@10.10.1.10:1180" }
# proxies={ "https": "https://10.10.1.10:1180" }
```
