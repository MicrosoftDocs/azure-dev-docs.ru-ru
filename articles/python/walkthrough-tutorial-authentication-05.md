---
title: Пошаговое руководство. Часть 5. Аутентификация приложений Python в службах Azure
description: В этой части описаны основные зависимости приложения (главным образом библиотеки Azure SDK), необходимые операторы импорта и переменные среды, которые нужно создать.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2da18ac4b1d27e976d0713fba16dbfc0ba644168
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379517"
---
# <a name="part-5-main-app-dependencies-import-statements-and-environment-variables"></a>Часть 5. Основные зависимости приложения, операторы импорта и переменные среды

[Предыдущая часть. Пример реализации основного приложения](walkthrough-tutorial-authentication-04.md)

В этой части рассматриваются библиотеки Python, добавляемые в основное приложение, и переменные среды, необходимые для кода. При развертывании в Azure вы используете параметры приложения в Службе приложений Azure, чтобы получить переменные среды.

## <a name="dependencies-and-import-statements"></a>Зависимости и операторы импорта

Коду приложения необходимы несколько библиотек: Flask, стандартная библиотека HTTP-запросов, а также библиотеки Azure для Active Directory ([azure.identity](/python/api/overview/azure/identity-readme?view=azure-python)), Key Vault ([azure.keyvault.secrets](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)) и Хранилища очередей ([azure.storage.queue](/python/api/overview/azure/storage-queue-readme?view=azure-python)). Эти библиотеки включены в файл приложения *requirements.txt*:

```txt
flask
requests
azure.identity
azure.keyvault.secrets
azure.storage.queue
```

При развертывании приложения в Службе приложений Azure эти необходимые компоненты автоматически устанавливаются на сервере узла. При локальном запуске вы устанавливаете их в своей среде с помощью команды `pip install -r requirements.txt`.

В верхней части кода находятся операторы импорта необходимых нам частей библиотек:

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient
from azure.storage.queue import QueueClient
```

## <a name="environment-variables"></a>Переменные среды

Код приложения зависит от четырех переменных среды:

| Переменная | Значение |
| --- | --- |
| THIRD_PARTY_API_ENDPOINT | URL-адрес стороннего API-интерфейса, например `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`, описанного в [Части 3](walkthrough-tutorial-authentication-03.md). |
| KEY_VAULT_URL | URL-адрес службы Azure Key Vault, в которой хранится ключ доступа для стороннего API. |
| THIRD_PARTY_API_SECRET_NAME | Имя секрета в Key Vault, содержащего ключ доступа для стороннего API. |
| STORAGE_QUEUE_URL | URL-адрес Хранилища очередей Azure, настроенного в Azure, например `https://msdocsmainappexample.queue.core.windows.net/code-requests` (см. [Часть 4](walkthrough-tutorial-authentication-04.md)). Так как имя очереди есть в конце URL-адреса, в коде оно не указывается. |

При локальном запуске вы создаете эти переменные в командной оболочке. При развертывании приложения на виртуальной машине такие переменные создаются на стороне сервера.

Но при развертывании в Службе приложений Azure у вас нет доступа к серверу. В этом случае вы создаете *параметры приложения* с такими же именами, которые выступают в роли переменных среды для приложения. 

В рамках скриптов подготовки эти параметры создаются с помощью команды Azure CLI [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set). Все четыре переменные задаются с помощью одной команды.

Сведения о создании параметров приложения на портале Azure см. в статье [Настройка приложения Службы приложений на портале Azure](/azure/app-service/configure-common).

> [!div class="nextstepaction"]
> [Часть 6. Код запуска основного приложения >>>](walkthrough-tutorial-authentication-06.md)
