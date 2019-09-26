---
title: Изучение файлов кода на Python в Функциях Azure с помощью Visual Studio Code
description: 'Руководство, шаг 3: основные сведения о шаблоне кода Python, который предоставляется в Функциях Azure.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 00/02/2019
ms.author: kraigb
ms.openlocfilehash: 10deffd63eeae22155f070e117e8f935990bcf93
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020012"
---
# <a name="examine-the-code-files"></a>Изучение файлов кода

[Предыдущий шаг: создание функции](tutorial-vs-code-serverless-python-02.md)

Во вложенной папке, которая создана для функции, находятся три файла: *\_\_init.\_\_py* содержит код функции, *function.json* содержит описание функции для Функций Azure, а *sample.dat* — это пример файла данных. Вы можете спокойно удалить *sample.dat*, так как он лишь напоминает о возможности добавлять другие файлы в эту вложенную папку.

Давайте начнем с изучения *function.json*, а затем перейдем к коду в файле *\_\_init\_\_.py*.

## <a name="functionjson"></a>function.json

Файл function.json file предоставляет необходимые сведения о конфигурации для конченой точки Функций Azure.

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

## <a name="__initpy__"></a>\_\_init.py\_\_

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
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Ниже описаны важные части этого кода.

- Необходимо импортировать `func` из `azure.functions`; импорт модуля журналов рекомендуется, но не является обязательным.
- Обязательная функция Python `main` получает объект `func.request` с именем `req` и возвращает значение с типом `func.HttpResponse`. Дополнительные сведения о возможностях этих объектов см. в документации [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) и [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python).
- Текст `main` обрабатывает сообщение и генерирует ответ. В нашем случае код ищет в URL-адресе параметр `name`. Если его нет, он проверяет наличие JSON в тексте запроса (с помощью `func.HttpRequest.get_json`) и наличие значения `name` в этом коде JSON (с помощью метода `get` из объекта JSON, который возвращает `get_json`).
- Если имя найдено, код возвращает строку "Hello" с найденным именем; в противном случае возвращается сообщение об ошибке.

> [!div class="nextstepaction"]
> [Я завершил изучение файлов кода](tutorial-vs-code-serverless-python-04.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=03-examine-code-files)