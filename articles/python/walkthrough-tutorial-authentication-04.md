---
title: Пошаговое руководство. Часть 4. Аутентификация приложений Python в службах Azure
description: В этой части приводятся общие сведения о реализации основного приложения и весь его код.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b7ffa1fb855d4b676813f49c545071209f6ce79
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379518"
---
# <a name="part-4-example-main-application-implementation"></a>Часть 4. Пример реализации основного приложения

[Предыдущая часть. Пример реализации стороннего API](walkthrough-tutorial-authentication-03.md)

В нашем сценарии основное приложение — это простое приложение Flask, которое развертывается в Службе приложений Azure. Приложение предоставляет общедоступную конечную точку API с именем */api/v1/getcode*, которая генерирует кодовую комбинацию для других задач, выполняемых приложением, например двухфакторной проверки подлинности для пользователей.

Чтобы проверить работу конечной точки, перейдите в браузере по адресу [https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode](https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode) или выполните по нему запрос в cURL.

Кроме того, основное приложение предоставляет простую домашнюю страницу, на которой отображается ссылка на конечную точку API. Работу этой части приложения можно проверить по адресу [https://msdocs-main-app-example.azurewebsites.net](https://msdocs-main-app-example.azurewebsites.net).

Скрипт подготовки примера отвечает за следующие действия:

1. Создание узла Службы приложений и развертывание кода с помощью команды Azure CLI [`az webapp up`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-up).

1. Подготовка учетной записи хранения Azure для основного приложения (с помощью [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)).

1. Создание очереди в учетной записи хранения с именем "code-requests" (с помощью [`az storage queue create`](/cli/azure/storage/queue?view=azure-cli-latest#az-storage-queue-create)).

1. Приложение должно иметь разрешение на запись в очередь. Выполните команду [`az role assignment create`](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create), чтобы назначить приложению роль "Участник для данных очереди хранилища". Дополнительные сведения о назначении ролей см. в статье о [назначении разрешения роли](how-to-assign-role-permissions.md).

Ниже показан код основного приложения. В следующих частях этого руководства подробно освещаются важные аспекты.

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
from azure.storage.queue import QueueClient

app = Flask(__name__)

# Retrieve the URL of the third-party API endpoint we invoke.
number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]

# Authenticate with Azure. First, obtain the credential object. To run locally,
# you must have the necessary environment variables set up to provide the
# local service principal information. When deployed to the cloud, the app must
# have managed identity enabled in Azure App Service.
credential = DefaultAzureCredential()

# Next, get a client object for the Key Vault. The client object is strictly
# a client-side construct provided by the Azure libraries as a layer on top
# of the Azure REST API.
key_vault_url = os.environ["KEY_VAULT_URL"]
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)

# Obtain the secret: for this step to work you must add the app's identity to
# the key vault's access policies for secret management. When deployed to the cloud
# the identity is the name of the app in App Service; when running locally, the
# identity is the local service principal.
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The access key
# we want for the third-party API is in the secret's value property.
access_key = vault_secret.value

#Set up the Storage queue client to which we write messages
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)


@app.route('/', methods=['GET'])
def home():
    return f'Home page of the main app. Make a request to <a href="./api/v1/getcode">/api/v1/getcode</a>.'


def random_char(num):
       return ''.join(random.choice(string.ascii_letters) for x in range(num))


@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code

    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }

    # Log a queue message with the code for, say, a process that invalidates
    # the code after a certain period of time.
    queue_client.send_message(code)

    return jsonify(code)


if __name__ == '__main__':
    app.run()
```

> [!div class="nextstepaction"]
> [Часть 5. Основные зависимости приложения, операторы импорта и переменные среды >>>](walkthrough-tutorial-authentication-05.md)
