---
title: Шаг 4. Локальная отладка кода Python в Функциях Azure с помощью VS Code
description: 'Руководство, шаг 4: локальный запуск отладчика VS Code для проверки кода Python.'
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seo-python-october2019
ms.openlocfilehash: 167e1c2be4d379f7457d35f2e6fe4a226092ac08
ms.sourcegitcommit: 089b87e1631a9db145583eb274edac6f80d16367
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708576"
---
# <a name="4-debug-the-azure-functions-python-code-locally"></a>4: Локальная отладка кода Python в решении "Функции Azure"

[Предыдущий шаг: изучение файлов кода](tutorial-vs-code-serverless-python-03.md)

Вы можете выполнять отладку кода Python для Функций Azure локально в Visual Studio Code.

1. Когда вы создаете проект Функций, расширение Visual Studio Code дополнительно создает в `.vscode/launch.json` одну конфигурацию запуска с именем **Attach to Python functions** (Вложение к функциям на Python). Наличие этой конфигурации означает, что осталось лишь нажать клавишу F5 или открыть обозреватель отладки, чтобы запустить проект.

    ![Настройка обозревателя отладки для запуска проекта Python](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

1. При запуске отладчика откроется окно терминала с результатами работы Функций Azure, включая список доступных конечных точек. URL-адрес может отличаться, если вы не использовали имя HttpExample:

    <pre>
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    </pre>

1. Щелчком мыши по URL-адресу при нажатой клавише **Ctrl** (или **Cmd**) в окне **Вывод** Visual Studio Code вы можете открыть этот адрес в браузере. Также можно перейти в браузер вручную и вставить в адресную строку этот URL-адрес. В обоих случаях используется конечная точка `api/<function_name>`, в нашем примере это `api/HttpExample`. Но поскольку этот URL-адрес не содержит параметра name (имя), в окне браузера отобразится текст "Please pass a name on the query string or in the request body" (Передайте имя в строке запроса или в тексте запроса), как требует соответствующий участок кода.

    > [!TIP]
    > Если вы не можете получить доступ к URL-адресу и процессы выполняются за корпоративным прокси-сервером (вероятнее всего, с заданными переменными среды `HTTP_PROXY` и `HTTPS_PROXY`), задайте для переменной среды с именем `NO_PROXY` значение `localhost,127.0.0.1` и повторите попытку.

1. Теперь попробуйте добавить параметр name, например со значением `http://localhost:7071/api/HttpExample?name=VS%20Code`. В окне браузера должно отобразиться сообщение "Hello Visual Studio Code!", что подтверждает выполнение этого пути кода.

1. Чтобы передать значение name в формате JSON в тексте запроса, примените инструмент curl и передайте ему встроенный текст JSON:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    В PowerShell также можно использовать командлет [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6).

    ---

    Также вы можете создать файл, например *data.json*, который содержит `{"name":"Visual Studio Code"}`, и выполнить команду `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Чтобы проверить отладку функции, установите точку останова в строке с текстом `name = req.params.get('name')` и снова выполните запрос к URL-адресу. Отладчик Visual Studio Code должен остановиться на указанной строке, что позволит вам проверять переменные и выполнить код по шагам. (Краткое пошаговое руководство по базовым возможностям отладки см. в [учебнике для Visual Studio Code по настройке и запуску отладчика](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger).)

1. Когда вы будете полностью удовлетворены результатами локального тестирования функции, закройте отладчик (с помощью команды меню **Отладка** > **Остановить отладку** или команды **Отключить** на панели инструментов отладки).

> [!NOTE]
> Если возникает ошибка, информирующая о том, что не удалось проверить подключение AzureWebJobsStorage, указанное в файле local.settings.json, это значит, что файл *local.settings.json* в проекте содержит строку `"AzureWebJobsStorage": "UseDevelopmentStorage=true"`. Эта строка указывает, что отладчик должен использовать эмулятор хранения Azure локально, но он не установлен. В этом случае вы можете [установить эмулятор хранения Azure](/azure/storage/common/storage-use-emulator#get-the-storage-emulator), а затем [запустить и инициализировать его](/azure/storage/common/storage-use-emulator#start-and-initialize-the-storage-emulator), а также перезапустить отладчик.
>
> Или вы можете изменить строку в файле JSON на `"AzureWebJobsStorage": ""` и перезапустить отладчик.

> [!div class="nextstepaction"]
> [Локальная работа с отладчиком завершена — перейти к шагу 5 >>>](tutorial-vs-code-serverless-python-05.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=04-test-debug)
