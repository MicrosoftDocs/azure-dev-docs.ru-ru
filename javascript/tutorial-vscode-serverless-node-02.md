---
title: Создание приложения Функций Azure в Visual Studio Code
description: Руководство, часть 2. Создание приложения Функций Azure
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 6c0adc93899eb9480008774fe35f3aa3b2ab5842
ms.sourcegitcommit: d9f585bea70b01ba6657a75ea245d8519d4a5aad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2020
ms.locfileid: "76967238"
---
# <a name="create-the-local-functions-app"></a>Создание локального приложения в службе "Функции"

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-serverless-node-01.md)

На этом этапе вы создадите в Функциях Azure локальное приложение, которое содержит функцию с [триггером HTTP](https://docs.microsoft.com/azure/azure-functions/functions-reference-node#http-triggers-and-bindings). Приложение Функций Azure может содержать несколько функций с [разными триггерами](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings). В частности, HTTP-триггер обрабатывает входящий трафик HTTP.

1. В окне терминала или командной строки запустите Visual Studio Code из соответствующей папки проекта:

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. В VS Code щелкните логотип Azure, чтобы открыть обозреватель **Функций Azure**, а затем выберите команду **Создать проект**.

    ![Создание локального приложения-функции в VS Code](media/functions-extension/create-function-app-project.png)

1. По двум первым запросам выберите текущую папку и язык **JavaScript**.

1. По запросу **Select a template for your project's first function** (Выберите шаблон для первой функции вашего проекта) укажите **HTTP-триггер**:

    ![Выбор триггера для функции](media/functions-extension/create-function-choose-template.png)

1. По запросу **Provide a function name** (Укажите имя функции) введите имя **HttpExample** (не используйте предложенный по умолчанию вариант "HttpTrigger", так как это имя будет совпадать с именем триггера, что может привести к путанице).

    ![Ввод имени функции](media/functions-extension/create-function-name.png)

1. По запросу выберите для параметра **Authorization Level** (Уровень авторизации) вариант **Anonymous** (Анонимный):

    ![Ввод имени функции](media/functions-extension/create-function-anonymous-auth.png)

1. Через несколько секунд в VS Code завершится создание проекта. Теперь у вас есть папка с именем, совпадающим с именем функции (*HttpExample*) и тремя файлами.

    | Имя файла | Описание |
    | --- | --- |
    | *index.js* |  Исходный код, который обрабатывает входящий HTTP-запрос. |
    | *functions.json* | [Конфигурация привязки](/azure/azure-functions/functions-triggers-bindings) для триггера HTTP. |
    | *sample.dat* | Пример файла данных, который демонстрирует возможность создавать другие файлы в этой папке. Вы можете спокойно удалить этот файл, так как он не используется в рамках нашего руководства. |

    ![Результат создания приложения-функции](media/functions-extension/create-function-app-results.png)

> [!div class="nextstepaction"]
> [Приложение Функций создано](tutorial-vscode-serverless-node-03.md)[Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
