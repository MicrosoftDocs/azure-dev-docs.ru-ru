---
title: Установка пакета Azure SDK для Go
description: Сведения о том, как выполнить установку, вендоринг и настройку пакета Azure SDK для Go.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/14/2018
ms.topic: conceptual
ms.devlang: go
ms.openlocfilehash: e7a4449ed29239e7cb08902cee299d74102a72bf
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68291944"
---
# <a name="install-the-azure-sdk-for-go"></a>Установка пакета Azure SDK для Go

Добро пожаловать в пакет Azure SDK для Go! Этот пакет SDK позволяет взаимодействовать со службами Azure и управлять ими из приложений Go.

## <a name="get-the-azure-sdk-for-go"></a>Получение пакета Azure SDK для Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

Некоторые службы Azure содержат собственные пакеты SDK Go, которые не включены в основной пакет Azure SDK для Go. В следующей таблице перечислены службы, содержащие собственные пакеты SDK, с указанием имен пакетов. Все эти пакеты доступны в режиме предварительной версии.

| Service | Package |
|---------|---------|
| Хранилище BLOB-объектов | [github.com/Azure/azure-storage-blob-go](https://github.com/Azure/azure-storage-blob-go) |
| Хранилище файлов | [github.com/Azure/azure-storage-file-go](https://github.com/Azure/azure-storage-file-go) |
| Очередь службы хранилища | [github.com/Azure/azure-storage-queue-go](https://github.com/Azure/azure-storage-queue-go) |
| Концентратор событий | [github.com/Azure/azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) |
| Служебная шина | [github.com/Azure/azure-service-bus-go](https://github.com/Azure/azure-service-bus-go) |

## <a name="vendor-the-azure-sdk-for-go"></a>Вендоринг пакета Azure SDK для Go

Выполнить вендоринг пакета Azure SDK для Go можно с помощью средства [dep](https://github.com/golang/dep). Чтобы обеспечить стабильность, мы рекомендуем использовать вендоринг. Для использования `dep` в проекте добавьте `github.com/Azure/azure-sdk-for-go` в раздел `[[constraint]]` файла `Gopkg.toml`. Например, чтобы выполнить вендоринг для версии `14.0.0`, добавьте следующую запись:

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a>Добавление пакета Azure SDK для Go в проект

Чтобы использовать службы Azure из кода Go, импортируйте все службы, с которыми вы взаимодействуете, и необходимые модули `autorest`.
Получить полный список доступных модулей для [доступных служб](https://godoc.org/github.com/Azure/azure-sdk-for-go) и [пакетов AutoRest](https://godoc.org/github.com/Azure/go-autorest) можно на сайте GoDoc. Ниже приводятся наиболее распространенные пакеты, которые нужно скачать в репозитории `go-autorest`:

| Package | ОПИСАНИЕ |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | Объекты для обработки аутентификации клиента службы |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | Константы для взаимодействия со службами Azure |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | Механизмы аутентификации для доступа к службам Azure |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | Вспомогательные приложения утверждения типа для работы со структурами данных пакета Azure SDK |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Версии пакетов Go и служб Azure управляются независимо друг от друга. Версии службы входят в путь импорта модуля (под модулем `services`). Полный путь к модулю состоит из имени службы, за которым следует версия в формате `YYYY-MM-DD` и снова имя службы. Например, чтобы импортировать версию `2017-03-30` службы вычислений, выполните следующий код:

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

При запуске разработки рекомендуется использовать и поддерживать последнюю версию службы.
Требования службы могут изменяться в разных версиях. Это может привести к ошибкам кода, даже если в это время обновления пакета SDK для Go не выполняются.

Если требуется общий моментальный снимок служб, вы также можете выбрать одну версию профиля. В нашем случае версия `2017-03-09` относится только к заблокированному профилю, который может не содержать последние версии служб и функций. Профили находятся в папке модуля `profiles` вместе с версиями в формате `YYYY-MM-DD`. Службы группируются по версиям профиля. Например, чтобы импортировать модуль управления ресурсами Azure из профиля `2017-03-09`, используйте следующий код:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> Существуют также профили `preview` и `latest`. Мы не рекомендуем использовать их. Это профили промежуточных версий, и поведение службы может измениться в любое время.

## <a name="next-steps"></a>Дополнительная информация

Чтобы начать работу с пакетом Azure SDK для Go, используйте код быстрого запуска.

* [Краткое руководство по развертыванию виртуальной машины Azure из шаблона с помощью пакета Azure SDK для Go](azure-sdk-go-qs-vm.md)
* [Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью Go](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [База данных Azure для PostgreSQL: подключение и запросы данных с помощью языка Go](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

Чтобы сразу приступить к работе с другими службами с помощью пакета SDK для Go, ознакомьтесь с доступными примерами кода.

* [Аутентификация с помощью служб Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [Развертывание новых виртуальных машин с использованием аутентификации по SSH](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Развертывание образа контейнера в службе "Экземпляры контейнеров Azure"](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Создание кластера в службе Azure Kubernetes](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [Работа со службами хранилища Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Все примеры для пакета Azure SDK для Go](https://github.com/azure-samples/azure-sdk-for-go-samples)
