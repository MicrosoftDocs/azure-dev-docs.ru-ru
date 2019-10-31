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
ms.openlocfilehash: e866d95acdd5d9e3a36b1a94efe624379e715dbe
ms.sourcegitcommit: 6cc225a6cec98bd51ef3c7bca05eeab210f2bbae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73142666"
---
# <a name="tutorial-debug-the-azure-functions-python-code-locally"></a>Руководство по Локальная отладка кода Python в решении "Функции Azure"

[Предыдущий шаг: изучение файлов кода](tutorial-vs-code-serverless-python-03.md)

Вы можете выполнять отладку кода Python для Функций Azure локально в Visual Studio Code.

1. Когда вы создаете проект Функций, расширение Visual Studio Code дополнительно создает в `.vscode/launch.json` одну конфигурацию запуска с именем **Attach to Python functions** (Вложение к функциям на Python). Наличие этой конфигурации означает, что осталось лишь нажать клавишу F5 или открыть обозреватель отладки, чтобы запустить проект.

    ![Настройка обозревателя отладки для запуска проекта Python](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

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
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
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
