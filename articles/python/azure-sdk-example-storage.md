---
title: Подготовка службы хранилища Azure с помощью библиотек Azure для Python
description: Узнайте, как с помощью библиотек из пакета Azure SDK для Python подготовить контейнер больших двоичных объектов в учетной записи хранения Azure, а затем передать файл в этот контейнер.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: ffc65746be0a7e002263010df4fe3048e0c321d1
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329672"
---
# <a name="example-use-the-azure-libraries-with-azure-storage"></a>Пример Использование библиотек Azure со службой хранилища Azure

Из этой статьи вы узнаете, как использовать библиотеки управления Azure в скрипте Python, чтобы создать группу ресурсов с учетной записью хранения Azure и контейнер хранилища BLOB-объектов. ([Эквивалентные команды Azure CLI](#for-reference-equivalent-azure-cli-commands) приведены далее в этой статье.)

После подготовки ресурсов см. [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md), чтобы использовать клиентские библиотеки Azure в коде приложения Python для отправки файла в контейнер хранилища больших двоичных объектов.

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Установка необходимых пакетов библиотеки Azure

1. Создайте файл *requirements.txt*, в котором перечислены библиотеки управления, используемые в этом примере.

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. В окне терминала с активированной виртуальной средой установите необходимые компоненты.

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Написание кода для подготовки ресурсов хранилища

В этом разделе описывается, как подготавливать ресурсы хранилища из кода Python. При желании можно также подготавливать ресурсы с помощью портала Azure или с помощью [эквивалентных команд Azure CLI](#for-reference-equivalent-azure-cli-commands).

Создайте файл Python с именем *provision_blob.py* со указанным ниже содержимым. Подробные объяснения даны в комментариях:

```python
import os, random

# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonAzureExample-Storage-rg"
LOCATION = "centralus"

# Step 1: Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: Provision the storage account, starting with a management object.

storage_client = get_client_from_cli_profile(StorageManagementClient)

# This example uses the CLI profile credentials because we assume the script
# is being used to provision the resource in the same way the Azure CLI would be used.

STORAGE_ACCOUNT_NAME = f"pythonazurestorage{random.randint(1,100000):05}"

# You can replace the storage account here with any unique name. A random number is used
# by default, but note that the name changes every time you run this script.
# The name must be 3-24 lower case letters and numbers only.


# Check if the account name is available. Storage account names must be unique across
# Azure because they're used in URLs.
availability_result = storage_client.storage_accounts.check_name_availability(STORAGE_ACCOUNT_NAME)

if not availability_result.name_available:
    print(f"Storage name {STORAGE_ACCOUNT_NAME} is already in use. Try another name.")
    exit()

# The name is available, so provision the account
poller = storage_client.storage_accounts.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME,
    {
        "location" : LOCATION,
        "kind": "StorageV2",
        "sku": {"name": "Standard_LRS"}
    }
)

# Long-running operations return a poller object; calling poller.result()
# waits for completion.
account_result = poller.result()
print(f"Provisioned storage account {account_result.name}")

# Step 3: Retrieve the account's primary access key and generate a connection string.
keys = storage_client.storage_accounts.list_keys(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME)

print(f"Primary key for storage account: {keys.keys[0].value}")

conn_string = f"DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={STORAGE_ACCOUNT_NAME};AccountKey={keys.keys[0].value}"

print(f"Connection string: {conn_string}")

# Step 4: Provision the blob container in the account (this call is synchronous)
CONTAINER_NAME = "blob-container-01"
container = storage_client.blob_containers.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME, CONTAINER_NAME, {})

# The fourth argument is a required BlobContainer object, but because we don't need any
# special values there, so we just pass empty JSON.

print(f"Provisioned blob container {container.name}")
```

Этот код использует методы проверки подлинности на основе CLI (`get_client_from_cli_profile`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте, лучше использовать `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности с использованием библиотек управления Azure для Python](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [StorageManagementClient (azure.mgmt.storage)](/python/api/azure-mgmt-storage/azure.mgmt.storage.storagemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4. Выполнение скрипта

```cmd
python provision_blob.py
```

Выполнение скрипта может занять одну-две минуты.

## <a name="5-verify-the-resources"></a>5: Проверка ресурсов

1. На [портале Azure](https://portal.azure.com) проверьте, созданы ли группа ресурсов и учетная запись хранения. Чтобы увидеть учетную запись хранения, созданную с помощью скрипта Python, в группе ресурсов, возможно, потребуется установить флажок **Показать скрытые типы**.

    ![Портал Azure, страница созданной группы ресурсов с учетной записью хранения](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Выберите учетную запись хранения, а затем в меню слева откройте раздел **Служба BLOB-объектов** > **Контейнеры**, чтобы проверить, создан ли контейнер bloc-container-01.

    ![Портал Azure, страница созданной учетной записи хранения с контейнером больших двоичных объектов](media/azure-sdk-example-storage/portal-show-blob-containers.png)

1. Если вы хотите использовать эти подготовленные ресурсы из кода приложения, ознакомьтесь со статьей [Пример. использования службы хранилища Azure](azure-sdk-example-storage-use.md).

Дополнительные примеры того, как можно использовать библиотеку управления службы хранилища Azure, см. в статье [Начало работы с пакетом управления службой хранилища Azure для Python](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в сценарии Python:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus ^
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg ^
    -n pythonazurestorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus \
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg \
    -n pythonazurestorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

---

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

Оставьте ресурсы на месте, если вы хотите выполнить инструкции в статье [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md), чтобы использовать эти ресурсы в коде приложения.

В противном случае выполните следующую команду, чтобы избежать дополнительных расходов в своей подписке.

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

## <a name="see-also"></a>См. также раздел

- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
