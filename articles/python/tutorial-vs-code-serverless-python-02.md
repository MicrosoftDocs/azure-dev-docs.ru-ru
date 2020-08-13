---
title: Шаг 2. Создание функции Python в Функциях Azure с помощью VS Code
description: 'Руководство, шаг 2: использование расширения "Функции Azure" для VS Code.'
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 13c0083434d024064462bb583a6b24d9e80c38dd
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983516"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Создание функции на Python в решении "Функции Azure"

[Предыдущий шаг: предварительные требования](tutorial-vs-code-serverless-python-01.md)

Узнайте, как создать функцию Python в Функциях Azure с помощью Visual Studio Code. Код для Функций Azure управляется через соответствующий _проект_, который создается перед началом работы с кодом.

1. В обозревателе **Azure: Функции** (открывается с помощью значка Azure в левой части экрана) щелкните значок команды **Создать проект** или откройте палитру команд (клавиша F1) и выберите **Функции Azure: создать проект**.

    ![Создание проекта в обозревателе Функций Azure](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. Выполните следующее в предложенных запросах:

    | prompt | Значение | Описание |
    | --- | --- | --- |
    | Укажите каталог для проекта. | Текущая открытая папка | Папка, в которой будет создаваться проект. Возможно, вы захотите создать проект во вложенной папке. |
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | **Python** | Язык, используемый для функции, от которого зависит шаблон для кода. |
    | Выбор интерпретатора Python | (Используйте указанный путь по умолчанию.) | Интерпретатор Python, используемый для виртуальной среды. |
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | **триггером HTTP** | Функция, которая использует триггер HTTP, выполняется при каждом получении HTTP-запроса к конечной точке функции. (Для Функций Azure существует много других триггеров. Подробнее см. статью [Что можно сделать с помощью Функций](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Provide a function name (Укажите имя функции) | HttpExample | Имя применяется к вложенной папке, которая содержит код функции и данные конфигурации, а также определяет имя конечной точки HTTP. Укажите HttpExample вместо предложенного по умолчанию варианта HTTPTrigger1, чтобы вы могли отличать функцию от триггера. |
    | Уровень авторизации | **Анонимный** | Анонимная авторизация делает функцию общедоступной для всех. |

1. Через некоторое время появится сообщение о том, что был создан новый проект. В **обозревателе** есть вложенная папка, созданная для функции, а Visual Studio Code открывает файл *\_\_init\_\_.py* со стандартным кодом функции:

    ![Результат создания проекта Python в Функциях Azure](media/tutorial-vs-code-serverless-python/display-results-of-new-python-project-in-azure-functions.png)

    Если Visual Studio Code при открытии файла *\_\_init\_\_.py* сообщит, что не выбран интерпретатор Python, откройте палитру команд (клавишей **F1**) и выберите команду **Python: Выбрать интерпретатор**, а затем выберите виртуальную среду в локальной папке `.env` (которая была создана как часть проекта).

    ![Выбор виртуальной среды, созданной с помощью проекта Python](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

> [!TIP]
> Если вы хотите создать другую функцию в том же проекте, используйте команду **Create Function** в обозревателе **Azure: Функции** или откройте палитру команд (клавишей **F1**) и выберите команду **Azure Functions: Create Function** (Функции Azure: создать функцию). Обе эти команды запрашивают имя функции (которое совпадает с именем конечной точки) и создают вложенную папку с файлами по умолчанию.
>
> ![Создание функций с помощью соответствующей функции в обозревателе Функций Azure](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Функция создана —перейти к шагу 3 >>>](tutorial-vs-code-serverless-python-03.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
