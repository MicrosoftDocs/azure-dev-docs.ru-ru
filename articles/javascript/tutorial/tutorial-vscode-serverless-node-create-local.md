---
title: Создание приложения Функций Azure в Visual Studio Code
description: Сведения о создании локального (бессерверного) приложения Функций Azure с функцией, которая использует триггер HTTP. Приложение Функций Azure может содержать несколько функций с разными триггерами. В частности, HTTP-триггер обрабатывает входящий трафик HTTP.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: devx-track-js, contperfq2
ms.openlocfilehash: 3c5d747245314029a12b1c502c066324efd70dc8
ms.sourcegitcommit: 550b165d0b910f4ea9652d8401dd4fc93f057f05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96605934"
---
# <a name="2-create-the-local-functions-app-with-the-visual-studio-code-_functions_-extension"></a>2. Создание локального приложения Функций с помощью расширения _Функций_ для Visual Studio Code

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-serverless-node-install.md)

На этом этапе вы создадите в Функциях Azure локальное (бессерверное) приложение, которое содержит функцию с [триггером HTTP](/azure/azure-functions/functions-reference-node#http-triggers-and-bindings). Приложение Функций Azure может содержать несколько функций с [разными триггерами](/azure/azure-functions/functions-triggers-bindings). В частности, HTTP-триггер обрабатывает входящий трафик HTTP.

1. В окне терминала или командной строки запустите Visual Studio Code из соответствующей папки проекта:

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. В Visual Studio Code щелкните логотип Azure, чтобы открыть обозреватель **Функций Azure**, а затем выберите команду **Создать проект**:

    ![Создание локального приложения-функции в VS Code](../media/functions-extension/create-function-app-project.png)

1. По двум первым запросам выберите текущую папку и язык **JavaScript**.

1. По запросу **Select a template for your project's first function** (Выберите шаблон для первой функции вашего проекта) укажите **HTTP-триггер**:

    ![Выбор триггера для функции](../media/functions-extension/create-function-choose-template.png)

1. По запросу **Provide a function name** (Укажите имя функции) введите имя **HttpExample** (не используйте предложенный по умолчанию вариант "HttpTrigger", так как это имя будет совпадать с именем триггера, что может привести к путанице).

    ![Ввод имени функции](../media/functions-extension/create-function-name.png)

1. По запросу выберите для параметра **Authorization Level** (Уровень авторизации) вариант **Anonymous** (Анонимный):

    ![ По запросу выберите для параметра "Уровень авторизации" вариант "Анонимный".](../media/functions-extension/create-function-anonymous-auth.png)

1. Через несколько секунд в VS Code завершится создание проекта. Теперь у вас есть папка с именем, совпадающим с именем функции (*HttpExample*) и тремя файлами.

    | Имя файла | Описание |
    | --- | --- |
    | *index.js* |  Исходный код, который обрабатывает входящий HTTP-запрос. |
    | *function.json* | [Конфигурация привязки](/azure/azure-functions/functions-triggers-bindings) для триггера HTTP. |
    | *sample.dat* | Пример файла данных, который демонстрирует возможность создавать другие файлы в этой папке. Вы можете спокойно удалить этот файл, так как он не используется в рамках нашего руководства. |

    ![Результат создания приложения-функции](../media/functions-extension/create-function-app-results.png)

## <a name="http-function-javascript-template-code"></a>Код шаблона JavaScript с функцией HTTP

Вам предоставляется базовый код для реагирования на HTTP-запрос. Если вы знакомы с объектами HTTP-запроса (параметр _req_) и ответа, вам будет знакома эта функция. Сведения ответа возвращаются с помощью объекта **context** свойства `res`.  

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

Каждый тип [триггера](/azure/azure-functions/functions-triggers-bindings?tabs=csharp) Функции предоставляет шаблон функции, что позволяет вам уделить больше вниманию коду своего приложения. При переходе с Express.js к Функциям Azure [просмотрите обязательные изменения](/azure/azure-functions/shift-expressjs?tabs=javascript) для своего приложения. 

> [!div class="nextstepaction"]
> [Приложение Функций создано](tutorial-vscode-serverless-node-test-local.md)[Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
