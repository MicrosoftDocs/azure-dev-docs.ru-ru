---
title: Шаг 3. Изучение файлов кода Python в Функциях Azure с помощью VS Code
description: 'Руководство, шаг 3: основные сведения о шаблоне кода Python, который предоставляется в Функциях Azure.'
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: a88c62bb017cc666b7c8e6c5cabe6df0768a17b0
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764525"
---
# <a name="3-examine-the-python-code-files-in-visual-studio-code"></a>3: Изучение файлов кода на Python в Visual Studio Code

[Предыдущий шаг: создание функции](tutorial-vs-code-serverless-python-02.md)

Найдите файлы Python в папке с функциями с помощью Visual Studio Code.

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
- Если имя найдено, код возвращает строку Hello с найденным именем; в противном случае он возвращает типовое сообщение.

> [!div class="nextstepaction"]
> [Файлы кода изучены — перейти к шагу 4 >>>](tutorial-vs-code-serverless-python-04.md)

