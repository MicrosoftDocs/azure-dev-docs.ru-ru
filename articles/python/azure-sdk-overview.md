---
title: SDK Azure для Python
description: Обзор функций и возможностей пакета Azure SDK для Python, которые позволяют повысить продуктивность разработчиков при подготовке, использовании и администрировании ресурсов Azure.
ms.date: 03/17/2020
ms.topic: conceptual
ms.openlocfilehash: 3d24a512420610f37285a03fe6a39d81e97510ee
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82026117"
---
# <a name="azure-sdk-for-python"></a>SDK Azure для Python

Пакет Azure SDK для Python с открытым кодом упрощает подготовку, использование и администрирование ресурсов Azure из кода приложения Python. Пакет SDK поддерживает Python 2.7 и Python 3.5.3 или более поздней версии.

Этот пакет SDK состоит из отдельных библиотек компонентов, каждая из которых устанавливается с помощью `pip install <library>`. Более подробные инструкции см. в [статье об установке пакета SDK](azure-sdk-install.md). Имена библиотек см. в [документации по пакету Azure SDK для Python](https://azure.github.io/azure-sdk-for-python/).

Вы также можете пройти пошаговое руководство [Начало работы с Azure SDK для Python](azure-sdk-get-started.yml) для самостоятельного знакомства с библиотеками.

> [!TIP]
> Сведения об изменениях в пакете SDK см. в [заметках о выпуске SDK](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="management-and-client-libraries"></a>Библиотеки управления и клиентские библиотеки

Пакет Azure SDK для Python содержит следующие два типа библиотек:

- *Библиотеки управления* позволяют подготавливать ресурсы Azure и управлять ими так же, как на [портале Azure](https://portal.azure.com) или с помощью средств командной строки, таких как [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и [Azure PowerShell](https://docs.microsoft.com/powershell/azure/). Библиотеки управления обычно используются в скриптах конфигурации и развертывания. (Что интересно, в коде Azure CLI используются эти самые библиотеки управления Python.)

- *Клиентские библиотеки* содержат средства для взаимодействия кода приложения с уже подготовленными службами с использованием собственных идиом Python. В коде клиентских библиотек используются интерфейсы REST API для Azure, но при использовании пакета SDK вам не придется беспокоиться о реализации REST.

В следующих разделах вы найдете дополнительные сведения и примеры для каждой из этих категорий.

## <a name="manage-azure-resources-with-management-libraries"></a>Управление ресурсами Azure с помощью библиотек управления

Библиотеки управления, входящие в пакет Azure SDK для Python (каждая из которых называется `azure-mgmt-<service name>`), помогут вам создавать, подготавливать и иным образом администрировать ресурсы Azure.

Например, предположим, что вам необходимо создать экземпляр SQL Server. Сначала установите соответствующую библиотеку управления:

```bash
pip install azure-mgmt-sql
```

В коде Python импортируйте библиотеку:

```python
from azure.mgmt.sql import SqlManagementClient
```

Затем создайте объект клиента управления, используя учетные данные (см. статью [о реализации проверки подлинности с помощью пакете SDK](azure-sdk-authenticate.md)) и идентификатор подписки Azure.

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Наконец, используйте этот объект клиента управления для создания ресурса, указав соответствующее имя группы ресурсов, имя сервера, расположение и учетные данные администратора.

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

Дополнительные сведения о работе с каждой библиотекой управления см. в файле *README.md* или *README.rst*, расположенном в папке проекта библиотеки в [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) пакета SDK. Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python) и в разделе с [примерами для Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="connect-and-use-azure-services-with-client-libraries"></a>Подключение к службам Azure и работа с ними с помощью клиентских библиотек

*Клиентские библиотеки*, входящие в пакет Azure SDK, позволяют подключаться к существующим ресурсам Azure и использовать их в своих приложениях, например для отправки файлов, доступа к данным таблиц или работы со службами Azure Cognitive Services. С пакетом SDK вы работаете с этими ресурсами, используя привычные парадигмы программирования Python, а не общий REST API службы. (Для служб, не имеющих REST API, нет соответствующей клиентской библиотеки.)

Предположим, что вы развернули веб-приложение в Службе приложений Azure и хотите добавить возможность отправки файла в учетную запись хранения Azure. Первым шагом является установка соответствующей библиотеки:

```bash
pip install azure-storage-blob
```

Далее загрузите библиотеку в код.

```python
from azure.storage.blob import BlobClient
```

Наконец, используйте API библиотеки для подключения и передачи данных. В этом примере строка подключения и имя контейнера уже подготовлены в вашей учетной записи хранения. Имя большого двоичного объекта — это имя, назначаемое переданным данным.

```python
blob = BlobClient.from_connection_string("my_connection_string", container_name="mycontainer", blob_name="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Дополнительные сведения о работе с каждой библиотекой управления см. в файле *README.md* или *README.rst*, расположенном в папке проекта библиотеки в [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) пакета SDK. Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python) и в разделе с [примерами для Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

### <a name="the-azure-core-library"></a>Библиотека ядра Azure

В настоящее время мы обновляем клиентские библиотеки, входящие в пакет Azure SDK для Python, чтобы применять в них основные облачные шаблоны проектирования, такие как протоколы проверки подлинности, ведение журналов, трассировка, транспортные протоколы, буферизация ответов и повторные попытки. Эта общая функциональная возможность содержится в библиотеке [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core). Библиотеки, которые в настоящее время работают с основной библиотекой, перечислены на странице со списком [последних выпусков пакета Azure SDK](https://azure.github.io/azure-sdk/releases/latest/#python-packages).

Дополнительные сведения об основной библиотеке и рекомендациях для нее см. в [руководстве по Python: введение](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="get-help-and-give-feedback"></a>Справка и отзывы

- См. [документацию по пакету SDK Azure для Python](https://aka.ms/python-docs)
- Задавайте вопросы сообществу на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Открытые проблемы с пакетом SDK на [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Упомянуть [@AzureSDK](https://twitter.com/AzureSdk/) в Twitter
