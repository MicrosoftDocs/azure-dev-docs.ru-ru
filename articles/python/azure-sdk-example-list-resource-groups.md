---
title: Перечисление групп ресурсов и ресурсов с помощью библиотек Azure для Python
description: Узнайте, как с помощью библиотеки управления ресурсами из пакета Azure SDK для Python перечислить группу ресурсов и ресурсы в ней.
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2867ea550ce233594d30b3de2be744d20a1e96de
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759594"
---
# <a name="example-use-the-azure-libraries-to-list-resource-groups-and-resources"></a>Пример Перечисление групп ресурсов и ресурсов с помощью библиотек Azure

В этом примере показано, как использовать библиотеки управления пакета Azure SDK в скрипте Python для выполнения двух задач:

- перечисление всех групп ресурсов в подписке Azure;
- перечисление ресурсов в определенной группе ресурсов.
 
Все описанные в этой статье команды работают одинаково как в Bash для Linux или macOS, так и в командных оболочках для Windows, если не указано иное.

[Эквивалентная команда Azure CLI](#for-reference-equivalent-azure-cli-commands) приведена далее в этой статье.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-azure-library-packages"></a>2: Установка пакетов библиотек Azure

Создайте файл *requirements.txt* со следующим содержимым.

```text
azure-mgmt-resource
azure-identity
```

В окне терминала или в командной строке с активированной виртуальной средой установите необходимые компоненты.

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-work-with-resource-groups"></a>3\. Написание кода для работы с группами ресурсов

### <a name="3a-list-resource-groups-in-a-subscription"></a>3.1. Перечисление групп ресурсов в подписке

Создайте файл Python с именем *list_groups.py* со следующим кодом. Подробные объяснения даны в комментариях:

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resource groups
group_list = resource_client.resource_groups.list()

# Show the groups in formatted output
column_width = 40

print("Resource Group".ljust(column_width) + "Location")
print("-" * (column_width * 2))

for group in list(group_list):
    print(f"{group.name:<{column_width}}{group.location}")
```

### <a name="3b-list-resources-within-a-specific-resource-group"></a>3.2. Перечисление ресурсов в определенной группе ресурсов

Создайте файл Python с именем *list_resources.py* со следующим кодом. Подробные объяснения даны в комментариях:

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resources in "myResourceGroup" (change to any name desired).
# The expand argument includes additional properties in the output.
resource_list = resource_client.resources.list_by_resource_group(
    "myResourceGroup",
    expand = "createdTime,changedTime"
)

# Show the resources in formatted output
column_width = 36

print("Resource".ljust(column_width) + "Type".ljust(column_width)
    + "Create date".ljust(column_width) + "Change date".ljust(column_width))
print("-" * (column_width * 4))

for resource in list(resource_list):
    print(f"{resource.name:<{column_width}}{resource.type:<{column_width}}"
       f"{str(resource.created_time):<{column_width}}{str(resource.changed_time):<{column_width}}")
```

### <a name="authentication-in-the-code"></a>Проверка подлинности в коде

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [AzureCliCredential (azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)

## <a name="4-run-the-scripts"></a>4\. Выполнение скриптов

Перечислите все группы ресурсов в подписке:

```cmd
python list_groups.py
```

Перечислите все ресурсов в группе ресурсов:

```cmd
python list_resources.py
```

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

Следующая команда Azure CLI перечисляет группы ресурсов в подписке с помощью выходных данных JSON:

```azurecli
az group list
```

Следующая команда перечисляет ресурсы в myResourceGroup в области centralus (аргумент location требуется для определения конкретного центра обработки данных):

```azurecli
az resource list --resource group myResourceGroup --location centralus
```

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка базы данных к работе и выполнение запросов к ней](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
- [Использование Управляемых дисков Azure с виртуальными машинами](azure-sdk-samples-managed-disks.md)
- [Пройдите короткий опрос об Azure SDK для Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)
