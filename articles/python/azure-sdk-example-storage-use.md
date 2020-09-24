---
title: Использование службы хранилища Azure с пакетом Azure SDK для Python
description: Узнайте, как с помощью библиотек из пакета Azure SDK для Python получить доступ к заранее подготовленному контейнеру больших двоичных объектов в учетной записи хранения Azure, а затем передать файл в этот контейнер.
ms.date: 08/05/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 161e27f1323053b45c687e60ade90e863ce71c64
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110491"
---
# <a name="example-access-azure-storage-using-the-azure-libraries-for-python"></a>Пример Доступ к службе хранилища Azure с помощью библиотек Azure для Python

Здесь показано, как использовать клиентские библиотеки Azure в коде приложения Python для отправки файла в контейнер хранилища больших двоичных объектов. В примере предполагается, что у вас подготовлены ресурсы, показанные в разделе [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md).

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, задайте переменные среды для субъекта-службы (см. ниже), а также создайте и активируйте виртуальную среду для этого проекта.

В этом примере предполагается, что вы задали следующие переменные среды.

| Имя переменной | Ожидаемое значение |
| --- | --- |
| AZURE_SUBSCRIPTION_ID | GUID подписки Azure. |
| AZURE_CLIENT_ID | Идентификатор клиента локального субъекта-службы. |
| AZURE_CLIENT_SECRET | Секрет клиента субъекта-службы. |
| AZURE_TENANT_ID | Идентификатор арендатора для субъекта-службы. |

## <a name="2-install-library-packages"></a>2: Установка пакетов библиотек

1. Добавьте строку для пакета необходимой клиентской библиотеки в файл *requirements.txt* и сохраните его.

    ```text
    azure-storage-blob
    azure-identity
    ```

1. В терминале или в командной строке выполните повторную установку необходимых компонентов.

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-create-a-file-to-upload"></a>3: Создание файла для отправки

Создайте исходный файл с именем *sample-source.txt* (как это предусмотрено кодом) со следующим содержимым:

```text
Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
```

## <a name="4-use-blob-storage-from-app-code"></a>4: Использование хранилища больших двоичных объектов из кода приложения

В следующих разделах (4а и 4б) показаны два способа доступа к контейнеру больших двоичных объектов, подготовленных с помощью инструкций из статьи [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md).

При использовании [первого способа (4а ниже)](#4a-use-blob-storage-with-authentication) аутентификация приложения выполняется с помощью `DefaultAzureCredential`, как описано в [разделе об аутентификации приложений Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential). При использовании этого метода необходимо сначала назначить соответствующие разрешения удостоверению приложения. Это также является рекомендуемым подходом.

Во [втором случае (4б ниже)](#4b-use-blob-storage-with-a-connection-string) используется строка подключения для прямого доступа к учетной записи хранения. Этот метод кажется проще. Но он имеет два существенных недостатка:

- Строка подключения лишь выполняет проверку подлинности агента для подключения к учетной записи *хранения*, а не к отдельным ресурсам в этой учетной записи. В результате этого строка подключения предоставляет больше разрешений, чем может потребоваться.

- Строка подключения содержит ключ доступа в виде обычного текста, а значит создает потенциальные уязвимости, если она неправильно скомпонована или ненадежно защищена. С помощью такой строки можно получить несанкционированный доступ к ряду ресурсов в учетной записи хранения.

По этим причинам рекомендуется использовать метод проверки подлинности в рабочем коде.

### <a name="4a-use-blob-storage-with-authentication"></a>4а. Доступ к хранилищу BLOB-объектов с проверкой подлинности

1. Создайте переменную среды с именем `STORAGE_BLOB_URL`.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    ---

    Замените "pythonazurestorage12345" именем своей учетной записи хранения.

    Эта переменная среды используется только в этом примере и не используется библиотеками Azure.

1. Создайте файл с именем *use_blob_auth.py* с указанным ниже кодом. Подробные объяснения даны в комментариях.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the Azure library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonazurestorage12345.blob.core.windows.net/
    storage_url = os.environ["STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

    Ссылки для справки:
      - [DefaultAzureCredential (azure.identity)](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python)
      - [BlobClient (azure.storage.blob)](/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)

1. Попытайтесь выполнить код (что должно завершиться сбоем):

    ```cmd
    python use_blob_auth.py
    ```

    Так как ваша локальная субъект-служба не имеет разрешения на доступ к контейнеру больших двоичных объектов, поступит сообщение об ошибке со сведениями о том, что у вызывающей стороны нет прав на выполнение этой операции.

1. Предоставьте контейнеру разрешения на доступ к субъекту-службе, выполнив команду Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (в которой довольно много текста).

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    Аргумент `--scope` определяет, к чему применяется это назначение ролей. В этом примере вы назначаете роль "Участник для данных BLOB-объектов хранилища" *конкретному* контейнеру с именем "blob-container-01".

    Замените `pythonazurestorage12345` точным именем своей учетной записи хранения. При необходимости можно также отредактировать имена группы ресурсов и контейнера больших двоичных объектов. Если вы неправильно укажите имя, то получите сообщение об ошибке "Cannot perform requested operation on nested resource. Parent resource 'pythonazurestorage12345' not found" (Не удается выполнить запрошенную операцию с вложенным ресурсом. Родительский ресурс pythonazurestorage12345 не найден).

    В этой команде аргумент `--scope` также использует переменные среды AZURE_CLIENT_ID и AZURE_SUBSCRIPTION_ID, которые уже должны быть заданы в локальной среде для субъекта-службы согласно инструкциям из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md).

1. Подождите 1–2 минуты, пока роль не будет назначена, и снова запустите код, чтобы убедиться, что он работает. Если ошибка прав доступа происходит снова, подождите еще и повторите попытку.

Дополнительные сведения см. в статье [Как назначить разрешения ролей удостоверению приложения или субъекту-службе](how-to-assign-role-permissions.md).

### <a name="4b-use-blob-storage-with-a-connection-string"></a>4б. Доступ к хранилищу BLOB-объектов с помощью строки подключения

1. Создайте переменную среды с именем `AZURE_STORAGE_CONNECTION_STRING`, значением которой является полная строка подключения для учетной записи хранения. (Эта переменная среды также используется различными комментариями Azure CLI.)

1. Создайте файл Python с именем *use_blob_conn_string.py* с указанным ниже содержимым. Подробные объяснения даны в комментариях.

    ```python
    import os

    # Import the client object from the Azure library
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

## <a name="5-verify-blob-creation"></a>5. Проверка создания большого двоичного объекта

После выполнения кода для первого или второго способа на [портале Azure](https://portal.azure.com) перейдите в контейнер больших двоичных объектов и проверьте, создан ли большой двоичный объект с именем *sample-blob.txt* с таким же содержимым, как в файле *sample-source.txt*.

![Портал Azure, страница контейнера больших двоичных объектов с загруженным файлом](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

```azurecli
az group delete -n PythonAzureExample-Storage-rg  --no-wait
```

Если вам не нужны ресурсы, созданные при работе с этим примером, выполните приведенную ниже команду, чтобы не нести расходы по подписке.

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Подготовка базы данных к работе и выполнение запросов к ней](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
