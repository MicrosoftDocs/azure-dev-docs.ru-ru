---
title: Руководство по Локальная отладка кода Python в Функциях Azure с помощью Visual Studio Code
description: 'Руководство, шаг 4: локальный запуск отладчика VS Code для проверки кода Python.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 324a67e5eebfce8fde7a2cf88d40f15864817df5
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172439"
---
# <a name="tutorial-debug-the-azure-functions-python-code-locally"></a>Руководство по Локальная отладка кода Python в решении "Функции Azure"

[Предыдущий шаг: изучение файлов кода](tutorial-vs-code-serverless-python-03.md)

1. Когда вы создаете проект Функций, расширение Visual Studio Code дополнительно создает в `.vscode/launch.json` одну конфигурацию запуска с именем **Attach to Python functions** (Вложение к функциям на Python). Наличие этой конфигурации означает, что осталось лишь нажать клавишу F5 или открыть обозреватель отладки, чтобы запустить проект.

    ![Обозреватель отладки с конфигурацией запуска функций](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. При запуске отладчика откроется окно терминала с результатами работы Функций Azure, включая список доступных конечных точек. URL-адрес может отличаться, если вы не использовали имя HttpExample:

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Щелчком мыши по URL-адресу при нажатой клавише **Ctrl** (или **Cmd**) в окне **Вывод** Visual Studio Code вы можете открыть этот адрес в браузере. Также можно перейти в браузер вручную и вставить в адресную строку этот URL-адрес. В обоих случаях используется конечная точка `api/<function_name>`, в нашем примере это `api/HttpExample`. Но поскольку этот URL-адрес не содержит параметра name (имя), в окне браузера отобразится текст "Please pass a name on the query string or in the request body" (Передайте имя в строке запроса или в тексте запроса), как требует соответствующий участок кода.

1. Теперь попробуйте добавить параметр name, например со значением `http://localhost:7071/api/HttpExample?name=VS%20Code`. В окне браузера должно отобразиться сообщение "Hello Visual Studio Code!", что подтверждает выполнение этого пути кода.

1. Чтобы передать значение name в формате JSON в тексте запроса, примените инструмент curl и передайте ему встроенный текст JSON:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Также вы можете создать файл, например *data.json*, который содержит `{"name":"Visual Studio Code"}`, и выполнить команду `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Чтобы проверить отладку функции, установите точку останова в строке с текстом `name = req.params.get('name')` и снова выполните запрос к URL-адресу. Отладчик Visual Studio Code должен остановиться на указанной строке, что позволит вам проверять переменные и выполнить код по шагам. (Краткое пошаговое руководство по базовым возможностям отладки см. в [учебнике для Visual Studio Code по настройке и запуску отладчика](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger).)

1. Когда вы будете полностью удовлетворены результатами локального тестирования функции, закройте отладчик (с помощью команды меню **Отладка** > **Остановить отладку** или команды **Отключить** на панели инструментов отладки).

> [!div class="nextstepaction"]
> [Я завершил локальную работу с отладчиком](tutorial-vs-code-serverless-python-05.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=04-test-debug)
