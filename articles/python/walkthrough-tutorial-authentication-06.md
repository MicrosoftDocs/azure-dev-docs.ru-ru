---
title: Пошаговое руководство. Часть 6. Аутентификация приложений Python в службах Azure
description: В этой части рассматривается код запуска основного приложения, который позволяет настроить объект DefaultAzureCredential и клиентские объекты, необходимые конечной точке API.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 8098b4effbf4d1d6e79b2f859a5f7eba7e68922f
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764710"
---
# <a name="part-6-main-app-startup-code"></a>Часть 6. Код запуска основного приложения

[Предыдущая часть. Зависимости, операторы импорта и переменные среды](walkthrough-tutorial-authentication-05.md)

Код запуска приложения, который следует за операторами `import`, инициализирует различные переменные, используемые в функциях, которые обрабатывают HTTP-запросы.

Сначала мы создадим объект приложения Flask и получим URL-адрес конечной точки стороннего API из переменной среды:

```python
app = Flask(__name__)

number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]
```

Затем мы получим объект [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential
), который рекомендуется использовать для аутентификации в службах Azure (см. статью об [аутентификации приложений Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential)).

```python
credential = DefaultAzureCredential()
```

При локальном запуске `DefaultAzureCredential` ищет переменные среды `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` и `AZURE_CLIENT_SECRET`, которые содержат сведения для локального субъекта-службы. При запуске в облаке `DefaultAzureCredential` автоматически использует зарегистрированный для приложения субъект-службу, который обычно содержится в управляемом удостоверении.

Затем с помощью кода из Azure Key Vault извлекается ключ доступа стороннего API. В рамках скрипта подготовки Key Vault создается с помощью команды [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create), а секрет сохраняется с помощью команды [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set).

Доступ к ресурсу Key Vault осуществляется по URL-адресу, который извлекается из переменной среды `KEY_VAULT_URL`.

```python
key_vault_url = os.environ["KEY_VAULT_URL"]
```

Чтобы подключиться к хранилищу ключей, необходимо создать подходящий клиентский объект. Так как мы хотим получить секрет, мы используем класс [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient), для которого требуется URL-адрес хранилища ключей и объект учетных данных. Такой объект представляет собой удостоверение для выполнения приложения.

```python
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)
```

Создание объекта `SecretClient` никоим образом не приводит к аутентификации объекта учетных данных. Объект `SecretClient` — это просто клиентская конструкция, которая внутренне управляет URL-адресом ресурса и объектом учетных данных. Аутентификация и авторизация происходят только при вызове операции через клиент, например с помощью метода [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient#get-secret-name--version-none----kwargs-), который создает вызов REST API к ресурсу Azure.

```python
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The key we
# want for the third-party API is in the value property. 
access_key = vault_secret.value
```

Даже если удостоверение приложения имеет право доступа к хранилищу ключей, ему необходимо специальное разрешение на доступ к секретам.  Иначе вызов `get_secret` завершится ошибкой. Поэтому скрипт подготовки задает политику доступа для метода get_secret приложения с помощью команды Azure CLI [`az keyvault set-policy`](/cli/azure/keyvault#az-keyvault-set-policy). Дополнительные сведения см. в статьях [Проверка подлинности в Azure Key Vault](/azure/key-vault/general/authentication) и [Назначение политики доступа к Key Vault на портале Azure](/azure/key-vault/general/managed-identity#grant-your-app-access-to-key-vault). Во второй статье показано, как задать политику доступа с помощью портала Azure (статья посвящена управляемым удостоверениям, но изложенные в ней сведения относятся и к локальным субъектам-службам, используемым при разработке).

Наконец, код приложения настраивает клиентский объект, посредством которого мы можем записывать сообщения в очередь службы хранилища Azure. URL-адрес очереди содержится в переменной среды `STORAGE_QUEUE_URL`.

```python
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)
```

Как и в случае с Key Vault, для подключения к ресурсу, расположенному по указанному URL-адресу, мы используем специальный клиентский объект из библиотек Azure [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) и его метод [`from_queue_url`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#from-queue-url-queue-url--credential-none----kwargs-). Как и в предыдущем случае, создание этого клиентского объекта всего лишь подтверждает, что удостоверение приложения, представленное объектом учетных данных, имеет право доступа к очереди. Как отмечалось ранее, разрешение на авторизацию было предоставлено основному приложению путем назначения ему роли "Участник для данных очереди хранилища".

При том условии, что весь этот код запуска будет выполнен успешно, приложение получит все внутренние переменные, необходимые для поддержки его конечной точки API */api/v1/getcode*.

> [!div class="nextstepaction"]
> [Часть 7. Конечная точка API основного приложения >>>](walkthrough-tutorial-authentication-07.md)
