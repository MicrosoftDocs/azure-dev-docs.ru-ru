---
title: Настройка ведения журнала в библиотеках Azure для Python
description: Библиотеки Azure используют стандартный модуль ведения журнала Python, который настраивается отдельно для каждой библиотеки или для каждой операции.
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0c08ba9c514bf9bca3c982ccf3ef3182f203e247
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983319"
---
# <a name="configure-logging-in-the-azure-libraries-for-python"></a>Настройка ведения журнала в библиотеках Azure для Python

Библиотеки Azure для Python, которые [основаны на странице azure.core](azure-sdk-library-package-index.md#libraries-using-azurecore), обеспечивают вывод журналов с помощью стандартной библиотеки [ведения журнала](https://docs.python.org/3/library/logging.html) Python.

Вывод средства ведения журнала не включен по умолчанию. Чтобы включить ведение журнала, сделайте следующее:

1. Получите объект ведения журнала для нужной библиотеки и установите уровень ведения журнала.
1. Зарегистрируйте обработчик для потока ведения журнала.
1. Включите ведение журнала, передав параметр `logging_enable=True` в конструктор клиентского объекта или в конкретный метод.

Обычно, чтобы разобраться в использовании ведения журнала в библиотеках, лучше всего просто изучить исходный код пакета SDK, который доступен по адресу [github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Мы рекомендуем клонировать этот репозиторий на локальный компьютер, чтобы легко найти нужные данные, когда потребуется (например, при работе со следующими разделами).

## <a name="set-logging-levels"></a>Настройка уровней ведения журнала

```python
import logging

# ...

# Acquire the logger for a library (azure.storage.blob in this example)
logger = logging.getLogger('azure.storage.blob')

# Set the desired logging level
logger.setLevel(logging.DEBUG)
```

- В этом примере запрашивается средство ведения журнала для библиотеки `azure.storage.blob`, а затем устанавливается уровень ведения журнала `logging.DEBUG`.

- Вы можете вызвать `logger.setLevel` в любое время, чтобы изменить уровень ведения журнала для разных сегментов кода.

Чтобы задать уровень для другой библиотеки, укажите в вызове `logging.getLogger` имя нужной библиотеки. Например, библиотека azure-eventhubs предоставляет средство ведения журнала с именем `azure.eventhubs`, а библиотека azure-storage-queue — с именем `azure.storage.queue`, и т. д. (В исходном коде пакета SDK часто используется инструкция `logging.getLogger(__name__)`, которая получает средство ведения журнала по имени модуля, где оно содержится.)

Кроме того, можно использовать более общие пространства имен. Например,

```python
import logging

# Set the logging level for all azure-storage-* libraries
logger = logging.getLogger('azure.storage')
logger.setLevel(logging.INFO)

# Set the logging level for all azure-* libraries
logger = logging.getLogger('azure')
logger.setLevel(logging.ERROR)
```

Чтобы выяснить, включен ли определенный уровень ведения журнала, можно использовать метод `logger.isEnabledFor`.

```python
print(f"Logger enabled for ERROR={logger.isEnabledFor(logging.ERROR)}, " \
    f"WARNING={logger.isEnabledFor(logging.WARNING)}, " \
    f"INFO={logger.isEnabledFor(logging.INFO)}, " \
    f"DEBUG={logger.isEnabledFor(logging.DEBUG)}")
```

Уровни ведения журнала совпадают со [стандартными уровнями библиотеки ведения журнала](https://docs.python.org/3/library/logging.html#levels). В следующей таблице собраны общие сведения об использовании этих уровней ведения журнала в библиотеках Azure для Python:

| Уровень ведения журнала             | Типичное применение |
| ---                       | ---         |
| logging.ERROR;             | Сбои, после которых приложение вряд ли восстановится (например, недостаточно памяти). |
| logging.WARNING (уровень по умолчанию) | Функция не может выполнить поставленную задачу (но не в тех случаях, когда эта функция может восстановить работу самостоятельно, как, например, при повторах вызова REST API). Функции обычно заносят предупреждение в журнал при создании исключений. Уровень предупреждений автоматически включает уровень ошибок. |
| logging.INFO              | Функция работает нормально или отменен вызов службы. Информационные события обычно содержат запросы, ответы и заголовки. Уровень информационных сообщений автоматически включает уровни ошибок и предупреждений. |
| logging.DEBUG             | Подробные сведения, которые обычно используются для устранения неполадок. Уровень отладки — единственный уровень ведения журнала, который сохраняет в заголовках конфиденциальные сведения, например ключи учетной записи. Выходные данные уровня отладки обычно содержат трассировку стека исключений. Уровень отладки автоматически включает уровни информационных сведений, предупреждений и ошибок. |
| logging.NOTSET            | Ведение журнала отключено. |

### <a name="library-specific-logging-level-behavior"></a>Поведение уровней ведения журнала для конкретных библиотек

Точное поведение на каждом уровне ведения журнала зависит от конкретной библиотеки. В некоторых библиотеках, например azure.eventhub, выполняется развернутое ведение журнала, а в других библиотек оно почти не используется.

Чтобы изучить принципы ведения журнала для конкретной библиотеки, лучше всего найти интересующие вас уровни ведения журнала в исходном коде пакета Azure SDK для Python.

1. В папке репозитория перейдите в папку *sdk*, а затем откройте папку для конкретной службы, которая вас интересует.

1. В этой папке найдите любую из следующих строк:

    - `_LOGGER.error`
    - `_LOGGER.warning`
    - `_LOGGER.info`
    - `_LOGGER.debug`

## <a name="register-a-log-stream-handler"></a>Регистрация обработчика потока журнала

Для сбора выходных данных журнала необходимо зарегистрировать в коде хотя бы один обработчик потока журнала:

```python
import logging

# Direct logging output to stdout. Without adding a handler,
# no logging output is captured.
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)
```

В этом примере регистрируется обработчик, который направляет выходные данные ведения журнала в stdout. Вы можете использовать и другие типы обработчиков, как описано в разделе [logging.handlers](https://docs.python.org/3/library/logging.handlers.html) в документации по Python.

## <a name="enable-logging-for-a-client-object-or-operation"></a>Включение ведения журнала для клиентского объекта или операции

Когда вы настроите уровень ведения журнала и зарегистрируете обработчик, нужно включить ведение журнала в библиотеке Azure для конструктора клиентского объекта или метода операции.

### <a name="enable-logging-for-a-client-object"></a>Включение ведения журнала для клиентского объекта

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential(), logging_enable=True)
```

При включении ведения журнала для клиентского объекта оно применяется для всех операций, которые вызываются через этот объект.

### <a name="enable-logging-for-an-operation"></a>Включение ведения журнала для операции

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Logging is not enabled on this client.
# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential())

# Enable logging for only this operation
client.create_container("container01", logging_enable=True)
```

## <a name="example-logging-output"></a>Пример выходных данных журнала

Следующий код мы уже видели в [примере использования учетной записи хранения](azure-sdk-example-storage-use.md), но в него добавлено ведение журнала на уровне отладки (комментарии опущены для краткости):

```python
import os, sys, logging
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobClient

logger = logging.getLogger('azure.storage.blob')
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

credential = DefaultAzureCredential()
storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential)

with open("./sample-source.txt", "rb") as data:
    blob_client.upload_blob(data, logging_enable=True)
```

Выходные данные ведения журнала выглядят так:

<pre>
Request URL: 'https://pythonsdkstorage12345.blob.core.windows.net/blob-container-01/sample-blob.txt'
Request method: 'PUT'
Request headers:
    'Content-Type': 'application/octet-stream'
    'Content-Length': '79'
    'x-ms-version': '2019-07-07'
    'x-ms-blob-type': 'BlockBlob'
    'If-None-Match': '*'
    'x-ms-date': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'User-Agent': 'azsdk-python-storage-blob/12.3.1 Python/3.8.3 (Windows-10-10.0.18362-SP0)'
    'Authorization': '*****'
Request body:
b"Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.\r\n"
Response status: 201
Response headers:
    'Content-Length': '0'
    'Content-MD5': 'kvMIzjEi6O8EqTVnZJNakQ=='
    'Last-Modified': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'ETag': '"0x8D8067EB52FF7BC"'
    'Server': 'Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0'
    'x-ms-request-id': '5df479b1-f01e-00d0-5b67-382916000000'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'x-ms-version': '2019-07-07'
    'x-ms-content-crc64': 'QmecNePSHnY='
    'x-ms-request-server-encrypted': 'true'
    'Date': 'Mon, 01 Jun 2020 22:54:14 GMT'
Response content:
</pre>
