---
title: SDK Azure для Python
description: Общие сведения о функциях и возможностях пакета SDK Azure для Python, способствующих производительности разработчиков при работе со службами Azure.
ms.date: 10/30/2019
ms.topic: conceptual
ms.openlocfilehash: 83a9541330f270e51ffa5ed8952a93dee8ff132f
ms.sourcegitcommit: ac68fb174d606c7af2bfa79fe32b8ca7b73c86a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946700"
---
# <a name="azure-sdk-for-python"></a>SDK Azure для Python

Пакет SDK Azure для Python упрощает использование ресурсов Azure и управление ими из кода приложения Python. Пакет SDK поддерживает Python 2.7 и Python 3.5.3 или более поздней версии.

Пакет SDK устанавливается путем установки отдельных библиотек компонентов с помощью `pip install <library>`. Имена библиотек для разных служб см. в документации по [Azure SDK для Python](https://azure.github.io/azure-sdk-for-python/).

См. сведения об установке библиотек и их импорте в проекты в руководстве по [установке пакета SDK](python-sdk-azure-install.md). Затем см. [Начало работы с разработкой в облаке с помощью библиотек Azure для Python](python-sdk-azure-get-started.yml), чтобы настроить аутентификацию и запустить пример кода в своей подписке Azure.

> [!TIP]
> Сведения об изменениях в пакете SDK см. в [заметках о выпуске SDK](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-and-use-azure-services"></a>Подключение и использование служб Azure

Несколько *клиентских библиотек* в пакете SDK помогают вам подключаться к существующим ресурсам Azure и использовать их в своих приложениях, таких как загрузка файлов, доступ к данным таблицы или работа с различными службами Azure Cognitive Services. С пакетом SDK вы работаете с этими ресурсами, используя привычные парадигмы программирования Python, а не общий REST API службы.

Например, предположим, что вы хотите передать большой двоичный объект в учетную запись хранения Azure, подготовленную ранее. Первым шагом является установка соответствующей библиотеки:

```bash
pip install azure-storage-blob
```

Далее загрузите библиотеку в код.

```python
from azure.storage.blob import BlobClient
```

Наконец, используйте API библиотеки для подключения и передачи данных. В этом примере строка подключения и имя контейнера уже подготовлены в вашей учетной записи хранения. Имя большого двоичного объекта — это имя, назначаемое переданным данным.

```python
blob = BlobClient.from_connection_string("my_connection_string", container="mycontainer", blob="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Дополнительные сведения о работе с каждой конкретной библиотекой см. в *README.md* или файле *README.rst*, расположенном в папке проекта библиотеки в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk). См. также доступные [примеры Azure](https://docs.microsoft.com/samples/browse/?languages=python).

Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python)

### <a name="the-azure-core-library"></a>Библиотека ядра Azure

В настоящее время мы обновляем клиентские библиотеки Python для совместного использования основных функций, таких как повторные попытки, ведение журнала, транспортные протоколы, протоколы проверки подлинности и т. д. Эта общая функциональная возможность содержится в библиотеке [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core). Дополнительные сведения о библиотеке и рекомендациях по ней см. в [руководстве по Python: введение](https://azure.github.io/azure-sdk/python_introduction.html).

Ниже перечислены библиотеки, которые в настоящее время работают с основной библиотекой.

- `azure-storage-blob`
- `azure-storage-queue`
- `azure-keyvault-keys`
- `azure-keyvault-secrets`

## <a name="manage-azure-resources"></a>Управление ресурсами Azure

Пакет SDK Azure для Python также содержит множество библиотек, которые помогут вам создавать, подготавливать и иным образом управлять ресурсами Azure. Мы будем называть их *библиотеками управления*. Каждая библиотека управления называется `azure-mgmt-<service name>`. С помощью библиотек управления можно написать код Python для выполнения тех же задач, которые можно выполнять с помощью [портала Azure](https://portal.azure.com) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Например, предположим, что вам необходимо создать экземпляр SQL Server. Сначала установите соответствующую библиотеку управления:

```bash
pip install azure-mgmt-sql
```

В коде Python импортируйте библиотеку:

```python
from azure.mgmt.sql import SqlManagementClient

```

Затем создайте объект клиента управления, используя учетные данные и идентификатор подписки Azure:

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Наконец, используйте этот объект клиента для создания ресурса, используя соответствующее имя группы ресурсов, имя сервера, расположение и учетные данные администратора:

```python
server = sql_client.servers.create_or_update(
    'myresourcegroup',
    'myservername',
    {
        'location': 'eastus',
        'version': '12.0', # Required for create
        'administrator_login': 'mysecretname', # Required for create
        'administrator_login_password': 'HusH_Sec4et' # Required for create
    }
)
```

Как и в случае с клиентскими библиотеками, сведения о работе с каждой библиотекой управления можно найти в файлах *README.md* или *README.rst*, расположенном в папке проекта библиотеки в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk).

Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python) 

## <a name="get-help-and-give-feedback"></a>Справка и отзывы

- См. [документацию по пакету SDK Azure для Python](https://aka.ms/python-docs)
- Задавайте вопросы сообществу на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Открытые проблемы с пакетом SDK на [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Напишите нам в Twitter [@AzureSDK](https://twitter.com/AzureSdk/)
