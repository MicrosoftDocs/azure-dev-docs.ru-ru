---
title: Подготовка и использование службы хранилища Azure с помощью пакета Azure SDK для Python
description: Узнайте, как с помощью библиотек из пакета Azure SDK для Python подготовить контейнер больших двоичных объектов в учетной записи хранения Azure, а затем передать файл в этот контейнер.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9b26dd4c5708231c807ea57979bed6bdcd9de25f
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2020
ms.locfileid: "83405107"
---
# <a name="example-use-the-azure-sdk-with-azure-storage"></a>Пример использования пакета Azure SDK со службой хранилища Azure

Из этой статьи вы узнаете, как использовать библиотеки управления Azure SDK в скрипте Python, чтобы создать группу ресурсов с учетной записью хранения Azure и контейнер хранилища BLOB-объектов. Кроме того, вы научитесь использовать клиентские библиотеки из пакета SDK Azure в коде приложения Python для отправки файла в контейнер хранилища BLOB-объектов.

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-needed-management-libraries"></a>2: Установка необходимых библиотек управления

1. Создайте файл *requirements.txt*, в котором перечислены библиотеки управления, используемые в этом примере.

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. В окне терминала с активированной виртуальной средой установите необходимые компоненты.

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Написание кода для подготовки ресурсов хранилища

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
RESOURCE_GROUP_NAME = "PythonSDKExample-Storage-rg"
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

STORAGE_ACCOUNT_NAME = f"pythonsdkstorage{random.randint(1,100000):05}"

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

## <a name="4-run-the-script"></a>4. Запустите скрипт.

```bash
python provision_blob.py
```

Выполнение скрипта может занять одну-две минуты.

## <a name="5-verify-the-resources"></a>5: Проверка ресурсов

1. На [портале Azure](https://portal.azure.com) проверьте, созданы ли группа ресурсов и учетная запись хранения. Чтобы увидеть учетную запись хранения, созданную с помощью скрипта Python, в группе ресурсов, возможно, потребуется установить флажок **Показать скрытые типы**.

    ![Портал Azure, страница созданной группы ресурсов с учетной записью хранения](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Выберите учетную запись хранения, а затем в меню слева откройте раздел **Служба BLOB-объектов** > **Контейнеры**, чтобы проверить, создан ли контейнер bloc-container-01.

    ![Портал Azure, страница созданной учетной записи хранения с контейнером больших двоичных объектов](media/azure-sdk-example-storage/portal-show-blob-containers.png)

Дополнительные примеры того, как можно использовать библиотеку управления службы хранилища Azure см. в статье [Начало работы с пакетом управления службой хранилища Azure для Python](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в сценарии Python:

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus \
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg \
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus ^
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg ^
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

---

## <a name="6-use-resources-through-the-sdk-client-libraries"></a>6\. Использование ресурсов с помощью клиентских библиотек из пакета SDK

В следующих разделах описаны два способа доступа к контейнеру больших двоичных объектов, который мы создали ранее. В них приводятся примеры отправки файла большого двоичного объекта в контейнер с помощью соответствующих клиентских библиотек из пакета SDK.

Выполните [общие для обоих способов действия](#common-steps-for-both-methods), чтобы поработать с кодом самостоятельно.

При использовании первого способа проверка подлинности приложения выполняется с помощью `DefaultAzureCredential`, как описано в разделе об [аутентификации приложений Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential). При использовании этого метода необходимо сначала назначить соответствующие разрешения удостоверению приложения. Это также является рекомендуемым подходом.

Во втором случае используется строка подключения для прямого доступа к учетной записи хранения. Этот метод кажется проще. Но он имеет два существенных недостатка:

- Строка подключения лишь выполняет проверку подлинности агента для подключения к учетной записи *хранения*, а не к отдельным ресурсам в этой учетной записи. В результате этого строка подключения предоставляет больше разрешений, чем может потребоваться.

- Строка подключения содержит ключ доступа в виде обычного текста, а значит создает потенциальные уязвимости, если она неправильно скомпонована или ненадежно защищена. С помощью такой строки можно получить несанкционированный доступ к ряду ресурсов в учетной записи хранения.

Поэтому в рабочем коде следует использовать вариант с проверкой подлинности, а для примера будет достаточно и строки подключения.

### <a name="common-steps-for-both-methods"></a>Общие для обоих способов действия

1. Добавьте строку для необходимых клиентских библиотек в файл *requirements.txt* и сохраните его.

    ```text
    azure-storage-blob
    azure-identity
    ```

1. В терминале или в командной строке выполните повторную установку необходимых компонентов.

    ```bash
    pip install -r requirements.txt
    ```

1. Создайте исходный файл с именем *sample-source.txt* (как это предусмотрено кодом) со следующим содержимым:

    ```text
    Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
    ```

### <a name="use-blob-storage-with-authentication"></a>Доступ к хранилищу BLOB-объектов с проверкой подлинности

1. Создайте переменную среды с именем `AZURE_STORAGE_BLOB_URL`.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    ---

    Замените pythonsdkstorage12345 именем своей учетной записи хранения.

1. Создайте файл с именем *use_blob_auth.py* с указанным ниже кодом. Подробные объяснения даны в комментариях.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonsdkstorage12345.blob.core.windows.net/
    storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Попытайтесь выполнить код.

    ```bash
    python use_blob_auth.py
    ```

    Так как ваша локальная субъект-служба не имеет разрешения на доступ к контейнеру больших двоичных объектов, поступит сообщение об ошибке со сведениями о том, что у вызывающей стороны нет прав на выполнение этой операции.

1. Чтобы предоставить субъекту-службе разрешение на доступ к контейнеру, выполните команду Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (в которой довольно много текста).

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    Аргумент `--scope` определяет, к чему применяется это назначение ролей. В этом примере вы назначаете роль "Участник для данных BLOB-объектов хранилища" *конкретному* контейнеру с именем "blob-container-01".

    Замените `pythonsdkstorage12345` точным именем своей учетной записи хранения. При необходимости можно также отредактировать имена группы ресурсов и контейнера больших двоичных объектов. Если вы неправильно укажите имя, то получите сообщение об ошибке "Cannot perform requested operation on nested resource. Parent resource 'pythonsdkstorage12345' not found" (Не удается выполнить запрошенную операцию с вложенным ресурсом. Родительский ресурс pythonsdkstorage12345 не найден).

    В этой команде аргумент `--scope` также использует переменные среды AZURE_CLIENT_ID и AZURE_SUBSCRIPTION_ID, которые уже должны быть заданы в локальной среде для субъекта-службы согласно инструкциям из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md).

1. Выполните код еще раз, чтобы убедиться, что он работает. Если ошибка прав доступа происходит снова, подождите некоторое время, пока роль не будет назначена, и повторите попытку.

Дополнительные сведения см. в статье [Как назначить разрешения ролей удостоверению приложения или субъекту-службе](how-to-assign-role-permissions.md).

### <a name="use-blob-storage-with-a-connection-string"></a>Доступ к хранилищу BLOB-объектов с помощью строки подключения

1. Создайте файл Python с именем *use_blob_conn_string.py* с указанным ниже содержимым. Подробные объяснения даны в комментариях.

    ```python
    import os

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    # Retrieve the connection string from an environment variable.
    conn_string = os.environ["AZURE_STORAGE_CONNECTION_STRING"]

    # Create the client object for the resource identified by the connection string,
    # indicating also the blob container and the name of the specific blob we want.
    blob_client = BlobClient.from_connection_string(conn_string, container_name="blob-container-01", blob_name="sample-blob.txt")

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Выполните код.

    ```bash
    python use_blob_conn_string.py
    ```

Как мы уже говорили, хотя этот метод прост, строка подключения дает разрешение на выполнение любых операций в учетной записи хранения. В рабочем коде лучше использовать специальные разрешения, как описано в предыдущем разделе.

### <a name="verify-blob-creation"></a>Проверка создания большого двоичного объекта

После выполнения кода для первого или второго способа на [портале Azure](https://portal.azure.com) перейдите в контейнер больших двоичных объектов и проверьте, создан ли большой двоичный объект с именем *sample-blob.txt* с таким же содержимым, как в файле *sample-source.txt*.

![Портал Azure, страница контейнера больших двоичных объектов с загруженным файлом](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="7-clean-up-resources"></a>7: Очистка ресурсов

```azurecli
az group delete -n PythonSDKExample-Storage-rg
```

Если вам не нужны ресурсы, созданные при работе с этим примером, выполните приведенную ниже команду, чтобы не нести расходы по подписке.

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Пример. Подготовка веб-приложения и развертывание кода >>>](azure-sdk-example-web-app.md)
