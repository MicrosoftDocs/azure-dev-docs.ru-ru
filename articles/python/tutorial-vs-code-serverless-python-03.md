---
title: Шаг 3. Изучение файлов кода Python в бессерверных Функциях Azure с помощью VS Code
description: Шаг 3 учебника. Основные сведения о шаблоне бессерверного кода Python, который предоставляется в Функциях Azure.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: a57d7bee924082a187b08c4326ae4de1d7cca998
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442194"
---
# <a name="3-examine-the-python-code-files-in-visual-studio-code"></a>3: Изучение файлов кода на Python в Visual Studio Code

[Предыдущий шаг: создание функции](tutorial-vs-code-serverless-python-02.md)

Созданная функция **HttpExample** включает три файла: *\_\_init\_\_.py* содержит код функции, *function.json* содержит описание функции для Функций Azure, а *sample.dat* — это пример файла данных. Вы можете спокойно удалить *sample.dat*, так как он лишь напоминает о возможности добавлять другие файлы в эту вложенную папку.

## <a name="functionjson"></a>function.json

Файл *function.json* предоставляет необходимые сведения о конфигурации для конечной точки Функций Azure:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Свойство `scriptFile` определяет файл запуска с кодом, который должен содержать функцию Python с именем `main`. Вы можете разделить код на несколько файлов, но указанный здесь файл обязательно должен содержать функцию `main`.

Элемент `bindings` содержит два объекта: один для описания входящих запросов, а другой — для описания ответа HTTP. Входящие запросы (`"direction": "in"`): наша функция реагирует на запросы HTTP GET или POST и не требует проверки подлинности. Ответы (`"direction": "out"`): это HTTP-ответ, который возвращает значение, полученное от функции Python `main`.

Дополнительные сведения о файле *functions.json* см. в [руководстве разработчика Функций Azure](/azure/azure-functions/functions-reference) и [справочных материалах по триггерах и привязкам Функций Azure](/azure/azure-functions/functions-triggers-bindings?tabs=python).

## <a name="__init__py"></a>\_\_init\_\_.py

Когда вы создаете новую функцию, решение "Функции Azure" предоставляет стандартный код Python в файле *\_\_init\_\_.py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
             "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
             status_code=200
        )
```

Ниже описаны важные части этого кода.

- Необходимо импортировать модуль `azure.functions`; импорт модуля журналов рекомендуется, но не является обязательным.
- Обязательная функция Python `main` получает объект `func.HttpRequest` с именем `req` и возвращает значение с типом `func.HttpResponse`. Дополнительные сведения о возможностях этих объектов см. в документации [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest) и [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse).
- Текст `main` обрабатывает сообщение и генерирует ответ. В нашем случае код ищет в URL-адресе параметр `name`. Если его нет, он проверяет наличие JSON в тексте запроса (с помощью `func.HttpRequest.get_json`) и наличие значения `name` в этом коде JSON (с помощью метода `get` из объекта JSON, который возвращает `get_json`).
- Если имя найдено, код возвращает строку "Hello" с найденным именем и уведомление об успешном выполнении функции. В противном случае код возвращает общее сообщение.

> [!div class="nextstepaction"]
> [Файлы кода изучены — перейти к шагу 4 >>>](tutorial-vs-code-serverless-python-04.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
