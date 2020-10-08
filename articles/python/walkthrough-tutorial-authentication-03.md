---
title: Пошаговое руководство. Часть 3. Аутентификация приложений Python в службах Azure
description: В этой части рассматривается пример реализации стороннего API с помощью Функций Azure и описывается способ защиты конечной точки с помощью ключа доступа.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 7c0098988265fef5b6b0f5e4a654f54c9bed4594
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764497"
---
# <a name="part-3-example-third-party-api-implementation"></a>Часть 3. Пример реализации стороннего API

[Предыдущая часть. Задачи аутентификации](walkthrough-tutorial-authentication-02.md)

В нашем примере сценария общедоступная конечная точка основного приложения использует сторонний API, защищенный ключом доступа. В этом разделе показана реализация стороннего API с помощью Функций Azure. Но API можно реализовать и по-другому и развернуть на другом облачном сервере или веб-узле. Главное здесь то, что конечная точка защищена определенным ключом доступа, который должен присутствовать во всех клиентских запросах. Таким образом, любое приложение, вызывающее этот API, должно безопасно управлять этим ключом.

Для наглядности мы развернем API в конечной точке `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`. Для его вызова необходимо предоставить ключ доступа `d0c5atM1cr0s0ft` либо в параметре URL-адреса `?code=`, либо в свойстве `'x-functions-key'` заголовка HTTP. Например, перейдите по этому URL-адресу в браузере или выполните по нему запрос в cURL: [https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft](https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft).

Если ключ доступа действителен, конечная точка возвращает ответ JSON, содержащий одно свойство "value". Его значением является число от 1 до 999, например `{"value": 959}`.

Конечная точка реализована на Python и развернута в Функциях Azure. Вот ее код:

```python
import logging
import random
import json

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('RandomNumber invoked via HTTP trigger.')

    random_value = random.randint(1, 1000)
    dict = { "value" : random_value }
    return func.HttpResponse(json.dumps(dict))
```

В репозитории примера этот код находится в разделе *third_party_api/RandomNumber/\_\_init\_\_.py*. Папка *RandomNumber* предоставляет имя функции, а *\_\_init\_\_.py* содержит код. Второй файл в этой папке, *function.json*, содержит описание того, когда вызывается функция. Другие файлы в родительской папке *third_party_api* содержат сведения о приложении-функции Функций Azure, которое включает саму функцию.

Скрипт подготовки примера отвечает за следующие действия для развертывания кода:

1. Создание резервной учетной записи хранения для Функций Azure с помощью команды Azure CLI [`az storage account create`](/cli/azure/storage/account#az-storage-account-create).

1. Создание приложения-функции Функций Azure с помощью команды [`az function app create`](/cli/azure/functionapp#az-functionapp-create).

1. Развертывание кода с помощью такой команды [Azure Functions Core Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash), как [`func azure functionapp publish`](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash#project-file-deployment), после паузы в 60 секунд, необходимой для завершения подготовки узла.

1. Назначение функции ключа доступа `d0c5atM1cr0s0ft` (дополнительные сведения о ключах доступа для функций см. в статье [Защита Функций Azure](/azure/azure-functions/security-concepts)).

    В рамках скрипта подготовки этот шаг выполняется путем вызова REST API к [API управления ключами Функций](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), так как Azure CLI сейчас не поддерживает эту функцию. Для вызова этого REST API скрипт подготовки сначала должен выполнить другой вызов REST API, чтобы получить главный ключ приложения-функции.

    Кроме того, ключи доступа можно назначить на [портале Azure](https://portal.azure.com). На странице приложения-функции выберите элемент **Функции**, а затем выберите функцию, которую нужно защитить ключом (в нашем примере это функция с именем `RandomNumber`). На странице функции выберите элемент **Function Keys** (Ключи функции), чтобы открыть страницу, на которой можно создавать такие ключи и управлять ими.

> [!div class="nextstepaction"]
> [Часть 4. Пример реализации основного приложения >>>](walkthrough-tutorial-authentication-04.md)
