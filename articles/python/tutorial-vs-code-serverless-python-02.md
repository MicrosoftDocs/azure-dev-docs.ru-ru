---
title: Шаг 2. Создание бессерверной функции Python в Функциях Azure с помощью VS Code
description: Шаг 2 руководства. Добавление бессерверной функции Python с помощью расширения Функций Azure для VS Code.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperfq2
ms.openlocfilehash: ac9bf8f90399a5ad2421689104fd5a6d9772c462
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96759311"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Создание функции на Python в решении "Функции Azure"

[Предыдущий шаг: настройка среды](tutorial-vs-code-serverless-python-01.md)

Из этой статьи вы узнаете, как создать функцию Python для бессерверных Функций Azure с помощью Visual Studio Code. Код для Функций Azure управляется через соответствующий _проект_, который создается перед началом работы с кодом.

1. В обозревателе **Azure: Функции** (открывается с помощью значка Azure в левой части экрана) щелкните значок команды **Создать проект** или откройте палитру команд (клавиша F1) и выберите **Функции Azure: создать проект**.

    ![Создание проекта в обозревателе Функций Azure](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. Выполните следующее в предложенных запросах:

    | prompt | Значение | Описание |
    | --- | --- | --- |
    | Выберите папку, содержащую ваш проект функций | Текущая открытая папка | Папка, в которой будет создаваться проект. Возможно, вы захотите создать проект во вложенной папке. |
    | Выбор языка | **Python** | Язык, используемый для функции, от которого зависит шаблон для кода. |
    | Select Python interpreter to create a virtual environment (Выберите интерпретатор Python для создания виртуальной среды) | (Используйте путь по умолчанию или вручную введите путь к подходящему интерпретатору, если он не указан.) | Интерпретатор Python, используемый для виртуальной среды. |
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | **триггером HTTP** | Функция, которая использует триггер HTTP, выполняется при каждом получении HTTP-запроса к конечной точке функции. (Для Функций Azure существует много других триггеров. Подробнее см. статью [Что можно сделать с помощью Функций](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Provide a function name (Укажите имя функции) | HttpExample | Имя применяется к вложенной папке, которая содержит код функции и данные конфигурации, а также определяет имя конечной точки HTTP. Укажите HttpExample вместо предложенного по умолчанию варианта HTTPTrigger1, чтобы вы могли отличать функцию от триггера. |
    | Уровень авторизации | **Анонимный** | Анонимная авторизация делает функцию общедоступной для всех. |

1. Этот процесс создает элемент **Локальный проект**, который отображается в обозревателе, с коллекцией с именем **Функции**. Такая коллекция включает функцию с указанным вами именем (**HttpExample** здесь):

    ![Представление обозревателя нового проекта Python в Функциях Azure](media/tutorial-vs-code-serverless-python/explorer-view-new-python-project-in-azure-functions.png)

    Если Visual Studio Code при открытии файла *\_\_init\_\_.py* сообщит, что не выбран интерпретатор Python, откройте палитру команд (клавишей **F1**) и выберите команду **Python: Выбрать интерпретатор**, а затем выберите виртуальную среду в локальной папке `.venv` (которая была создана как часть проекта).

    ![Выбор виртуальной среды, созданной с помощью проекта Python](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

1. Редактор VS Code также открывает файл *\_\_init\_\_.py* с кодом функции по умолчанию. В верхней части редактора располагается элемент управления **HttpExample > __init.py__**:

    ![Элемент управления с селектором файлов для функции в VS Code](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-01.png)

    Если выбрать **__init.py__**, VS Code отображает все файлы функции: *\_\_init\_\_.py* с кодом, *function.json* с конфигурацией и *sample.dat* с примером данных. Вы можете выбрать любой из файлов для переключения между ними:

    ![Элемент управления с селектором файлов для файлов кода функции](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-02.png)

    Аналогично, если выбрать **HttpExample**, VS Code отображает все файлы и папки на уровне *проекта*:

    ![Элемент управления с селектором проектов для функции в VS Code](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-03.png)

> [!TIP]
> Если вы хотите создать другую функцию в том же проекте, используйте команду **Create Function** в обозревателе **Azure: Функции** или откройте палитру команд (клавишей **F1**) и выберите команду **Azure Functions: Create Function** (Функции Azure: создать функцию). Обе команды запрашивают имя функции (которая является именем конечной точки), а затем создает другую вложенную папку функции в проекте с собственными файлами *\_\_init\_\_.py*, *function.json* и *sample.dat*.
>
> ![Создание функций с помощью соответствующей функции в обозревателе Функций Azure](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Функция создана —перейти к шагу 3 >>>](tutorial-vs-code-serverless-python-03.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
