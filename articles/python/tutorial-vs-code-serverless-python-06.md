---
title: Шаг 6. Добавление второй бессерверной функции Python в Функциях Azure с помощью VS Code
description: Шаг учебника 6. Расширение проекта Функций Azure путем добавления второй бессерверной функции.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperfq2
ms.openlocfilehash: ac1aafc1ccaafd893a66e8d4ac0a8ee247c12dc3
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96759411"
---
# <a name="6-add-a-second-python-function-to-azure-functions"></a>6\. Добавление второй функции на Python в решении "Функции Azure"

[Предыдущий шаг: развертывание в Azure](tutorial-vs-code-serverless-python-05.md)

После исходного развертывания вы можете внести изменения в код, например добавить дополнительные функции Python, и выполнить повторное развертывание в том же приложении Функций Azure.

1. В области **Azure: Функции** щелкните команду **Create Function** (Создание функции) или откройте **Azure Functions: Create Function** (Функции Azure: создание функции) на палитре команд. Укажите для функции следующие сведения.

    - Шаблон: Триггер HTTP
    - Имя: "DigitsOfPi"
    - Уровень авторизации: Анонимные

    В разделе **Локальный проект** в обозревателе Функций Azure теперь отображается функция *DigitsOfPi*. В редакторе можно переключаться между файлами *\_\_init\_\_.py*, *function.json* и *sample.dat* функции.

1. Замените содержимое файла *\_\_init\_\_.py* приведенным ниже кодом, который создает строку с числовым значением PI до такого количества цифр, которое указано в URL-адресе (этот код принимает только параметр URL-адреса):

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

        return output

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

1. Запустите локальный отладчик, нажав клавишу F5 или выбрав команду меню **Выполнить** > **Начать отладку**. В **окне вывода** должны отобразиться обе конечные точки проекта.

    <pre>
    Http Functions:
            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    </pre>

1. В браузере или с помощью средства curl создайте запрос к адресу `http://localhost:7071/api/DigitsOfPi?digits=125` и просмотрите выходные данные. (Вы можете заметить неточности в алгоритме этого кода и самостоятельно усовершенствовать его.) Завершив работу, остановите отладчик.

1. Повторно разверните код, используя команду **Deploy to Function App** (Развертывание в приложение-функция) в обозревателе **Azure: Функции**. Выберите созданное ранее приложение-функцию, если появится соответствующий запрос.

1. Через несколько минут развертывание будет завершено, и в **окне вывода** отобразятся общедоступные конечные точки, с которыми вы теперь можете повторить тесты.

> [!div class="nextstepaction"]
> [Вторая функция добавлена — перейти к шагу 7 >>>](tutorial-vs-code-serverless-python-07.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
