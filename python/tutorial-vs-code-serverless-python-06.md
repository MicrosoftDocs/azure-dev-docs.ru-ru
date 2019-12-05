---
title: Руководство по Добавление второй функции на Python в Функциях Azure с помощью Visual Studio Code
description: 'Шаг руководства 6: расширение проекта Функций Azure путем добавления второй функции.'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 84130e3bc4d5a85046e21b66338ccc07bba8cad1
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465916"
---
# <a name="tutorial-add-a-second-python-function-to-azure-functions"></a>Руководство по Добавление второй функции на Python в решении "Функции Azure"

[Предыдущий шаг: развертывание в Azure](tutorial-vs-code-serverless-python-05.md)

После исходного развертывания вы можете внести изменения в код Python, например добавить дополнительные функции, и выполнить повторное развертывание в том же приложении Функций Azure.

1. В области **Azure: Функции** щелкните команду **Create Function** (Создание функции) или откройте **Azure Functions: Create Function** (Функции Azure: создание функции) на палитре команд. Укажите для функции следующие сведения.

    - Шаблон: Триггер HTTP
    - Имя: "DigitsOfPi"
    - Уровень авторизации: Анонимные

1. В обозревателе файлов Visual Studio Code есть вложенная папка, имя которой совпадает с именем функции и в которой размещены уже знакомые файлы *\_\_init\_\_.py*, *function.json* и *sample.dat*.

1. Замените содержимое файла *\_\_init\_\_.py* приведенным ниже кодом, который создает строку с числовым значением PI до такого количества цифр, которое указано в URL-адресе (этот код принимает только параметр URL-адреса).

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Так как код поддерживает только HTTP GET, измените *function.json*, чтобы коллекция `"methods"` содержала только `"get"` (то есть удалите значение `"post"`). Теперь этот файл должен выглядеть следующим образом:

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
            "get"
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

1. Запустите отладчик, нажав клавишу F5 или выбрав команду меню **Отладка** > **Начать отладку**. В **окне вывода** должны отобразиться обе конечные точки проекта.

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. В браузере или с помощью средства curl создайте запрос к адресу `http://localhost:7071/api/DigitsOfPi?digits=125` и просмотрите выходные данные. (Вы можете заметить неточности в алгоритме этого кода и самостоятельно усовершенствовать его.) Завершив работу, остановите отладчик.

1. Повторно разверните код, используя команду **Deploy to Function App** (Развертывание в приложение-функция) в обозревателе **Azure: Функции**. Выберите созданное ранее приложение-функцию, если появится соответствующий запрос.

1. По завершении развертывания (этот процесс займет несколько минут) в **окне вывода** отобразятся общедоступные конечные точки, с которыми вы теперь можете повторить тесты.

> [!div class="nextstepaction"]
> [Я добавил вторую функцию](tutorial-vs-code-serverless-python-07.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=06-second-function)
