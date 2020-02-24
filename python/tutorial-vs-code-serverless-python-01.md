---
title: Руководство по Создание и развертывание бессерверных функций Azure на Python с помощью VS Code
description: 'Руководство, шаг 1: общие сведения и предварительные требования.'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 3056ccaa6d0cc8e5f2e15c6b7511172f59644936
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422189"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Руководство по Создание и развертывание бессерверных функций Azure на языке Python с помощью Visual Studio Code

В этой статье объясняется, как с помощью Visual Studio Code и расширения Функций Azure создать бессерверную конечную точку HTTP с поддержкой Python, а также как подключить (создать привязку) к хранилищу.

Функции Azure выполняют код в бессерверной среде без необходимости создавать виртуальную машину или публиковать веб-приложение. Расширение "Функции Azure" для Visual Studio Code значительно упрощает процесс использования функций, автоматически обрабатывая многие вопросы конфигурации.

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте кнопку **У меня есть проблема** в конце каждой статьи, чтобы отправить отзыв.

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Основные инструменты службы "Функции Azure"](#azure-functions-core-tools).
- [Visual Studio Code с расширением "Функции Azure"](#visual-studio-code-python-and-the-azure-functions-extension).

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) и получите 30-дневную бесплатную учетную запись с деньгами на счете Azure в размере 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Установите Azure Functions Core Tools, следуя [соответствующим инструкциям](/azure/azure-functions/functions-run-local#v2) для своей операционной системы. Игнорируйте комментарии в статье о диспетчере пакетов Chocolately. Они не нужны для работы с этим руководством.

При установке Node.js используйте параметры по умолчанию и *не* выбирайте параметр для автоматической установки необходимых средств.  Кроме того, обязательно используйте параметр `-g` с командами `npm install`, чтобы средства Core Tools были доступны для последующих команд.

    > [!TIP]
    > The Core Tools are written in .NET Core, and the Core Tools package is best installed using the Node.js package manager, npm, which is why you need to install .NET Core and Node.js at present, even for working with Azure Functions in Python. You can, however bypass the .NET Core requirement using "extension bundles" as described in the aforementioned documentation. Whatever the case, you need install these components only once, after which Visual Studio Code automatically prompts you to install any updates.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python и расширение "Функции Azure"

Установите следующее программное обеспечение:

- Python 3.7 или Python 3.6 для работы с Функциями Azure. [Python 3.7.5](https://www.python.org/downloads/release/python-375/) и [Python 3.6.8](https://www.python.org/downloads/release/python-368/) — это новейшие совместимые версии. Прокрутите страницы вниз, чтобы найти установщики. При установке выберите **Add Python 3.x to PATH** (Добавить в путь Python 3.x). Используйте параметры по умолчанию, выбрав **Установить сейчас**. В Windows также выберите **Disable Path length limit** (Отключить ограничение длины пути) в конце процесса.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительных требований](https://code.visualstudio.com/docs/python/python-tutorial) учебника по Python в VS Code.
- [Расширение "Функции Azure"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Общие сведения см. в репозитории [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions) на сайте GitHub.

    > [!NOTE]
    > Расширение "Функции Azure" входит в состав [пакета расширений Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>проверка предварительных требований;

Чтобы убедиться, что набор средств Azure Functions Core Tools установлен, откройте палитру команд Visual Studio Code (клавишей **F1**) и выберите команду **Терминал: Создание нового интегрированного терминала**. Когда откроется терминал, выполните команду `func`.

![Проверка предварительных требований для Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

Выходные данные, которые начинаются с логотипа Функций Azure (чтобы его увидеть, прокрутите выходные данные вверх), указывают на наличие Azure Functions Core Tools.

Если команда `func` не распознана, снова выполните команду `npm install -g azure-functions-core-tools` и убедитесь, что установка прошла успешно. Кроме того, обязательно используйте параметр `-g` с командой install, иначе npm установит пакет только в текущей папке.

Команда `func` работает с файлом *func. cmd*, установленным в глобальной папке Node.js. Чтобы узнать расположение этой папки, выполните команду `npm -l` и просмотрите соответствующие сведения в конце выходных данных.

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-vs-code-serverless-python-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
