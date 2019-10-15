---
title: Руководство по Создание функции на Python в Функциях Azure с помощью Visual Studio Code
description: 'Руководство, шаг 2: использование расширения "Функции Azure" для VS Code.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 691e64ae9b407ba4277ddde2a62a583623e53484
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172485"
---
# <a name="tutorial-create-a-python-function-for-azure-functions"></a>Руководство по Создание функции на Python в решении "Функции Azure"

[Предыдущий шаг: предварительные требования](tutorial-vs-code-serverless-python-01.md)

1. Код для Функций Azure управляется через _проект_, который создается перед началом работы с кодом. В обозревателе **Azure: Функции** (открывается значком Azure в левой части экрана) выберите значок команды **Создать проект** или откройте палитру команд (клавишей F1) и выберите **Функции Azure: создать проект**.

    ![Кнопка "Создать новый проект" в обозревателе Функций](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Выполните следующее в предложенных запросах:

    | prompt | Значение | ОПИСАНИЕ |
    | --- | --- | --- |
    | Укажите каталог для проекта. | Текущая открытая папка | Папка, в которой будет создаваться проект. Возможно, вы захотите создать проект во вложенной папке. |
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | **Python** | Язык, используемый для функции, от которого зависит шаблон для кода. |
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | **триггером HTTP** | Функция, которая использует триггер HTTP, выполняется при каждом получении HTTP-запроса к конечной точке функции. (Для Функций Azure существует много других триггеров. Подробнее см. статью [Что можно сделать с помощью Функций](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Provide a function name (Укажите имя функции) | HttpExample | Имя применяется к вложенной папке, которая содержит код функции и данные конфигурации, а также определяет имя конечной точки HTTP. Укажите имя "HttpExample" вместо предложенного по умолчанию "HTTPTrigger", чтобы отличать функцию от триггера. |
    | Уровень авторизации | **Анонимный** | Анонимная авторизация делает функцию общедоступной для всех. |
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | **Открыть в текущем окне** | Открывает проект в текущем окне Visual Studio Code. |

1. Через некоторое время появится сообщение о том, что был создан новый проект. В **обозревателе** есть вложенная папка, созданная для функции, а Visual Studio Code открывает файл *\_\_init\_\_.py* со стандартным кодом функции:

    [![Результат создания проекта функции на Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Если Visual Studio Code при открытии файла *\_\_init\_\_.py* сообщит, что не выбран интерпретатор Python, откройте палитру команд (клавишей **F1**) и выберите команду **Python: Выбрать интерпретатор**, а затем выберите виртуальную среду в локальной папке `.env` (которая была создана в составе проекта). Среда должна основываться на Python 3.6.x, как указано в разделе [Предварительные требования](tutorial-vs-code-serverless-python-01.md#prerequisites) предыдущей статьи.
    >
    > ![Выбор виртуальной среды, созданной для проекта](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Если вы хотите создать другую функцию в том же проекте, используйте команду **Create Function** в обозревателе **Azure: Функции** или откройте палитру команд (клавишей **F1**) и выберите команду **Azure Functions: Create Function** (Функции Azure: создать функцию). Обе эти команды запрашивают имя функции (которое совпадает с именем конечной точки) и создают вложенную папку с файлами по умолчанию.
>
> ![Команда "Новая функция" в обозревателе "Azure: Функции"](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Я создал функцию](tutorial-vs-code-serverless-python-03.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
