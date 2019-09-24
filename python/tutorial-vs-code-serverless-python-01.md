---
title: Создание и развертывание бессерверных функций Azure на языке Python с помощью Visual Studio Code
description: 'Руководство, шаг 1: общие сведения и предварительные требования.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 84971f6befb7210052ee3b144533715d0c6d1fb3
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020082"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Развертывание Python в Функциях Azure с помощью Visual Studio Code

В этом руководстве вы примените Visual Studio Code и расширение функций Azure, чтобы создать бессерверную конечную точку HTTP с поддержкой Python и подключение ("привязку") к хранилищу. Функции Azure выполняют код в бессерверной среде без необходимости создавать виртуальную машину или публиковать веб-приложение. Расширение "Функции Azure" для Visual Studio Code значительно упрощает процесс использования функций, автоматически обрабатывая многие вопросы конфигурации.

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте кнопку **У меня есть проблема** в конце каждой статьи, чтобы отправить отзыв.

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code с расширением "Функции Azure"](#visual-studio-code-python-and-the-azure-functions-extension).
- [Основные инструменты службы "Функции Azure"](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) и получите 30-дневную бесплатную учетную запись с деньгами на счете Azure в размере 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python и расширение "Функции Azure"

Установите следующее программное обеспечение:

- Python 3.6.x, который требуется для работы с Функциями Azure. Последняя версия 3.6.x — это [Python 3.6.8](https://www.python.org/downloads/release/python-368/).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительных требований](https://code.visualstudio.com/docs/python/python-tutorial) учебника по Python в VS Code.
- [Расширение "Функции Azure"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Общие сведения см. в репозитории [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions) на сайте GitHub.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Следуйте инструкциям по [работе с Azure Functions Core Tools](/azure/azure-functions/functions-run-local.md#v2) для вашей операционной системы. Сами средства написаны на языке .NET Core, а пакет основных средств лучше всего устанавливать с помощью диспетчера пакетов npm из набора Node.js, а значит вам пока придется устанавливать .NET Core и Node.js даже для кода на Python. Но вы можете обойтись без .NET Core, используя "пакеты расширений", как описано в упомянутой выше документации. В любом случае все эти компоненты нужно устанавливать только один раз, после чего Visual Studio Code будет автоматически предлагать вам все нужные обновления.

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>проверка предварительных требований;

Чтобы убедиться, что набор средств Azure Functions Core Tools установлен, откройте палитру команд Visual Studio Code (клавишей **F1**) и выберите команду **Терминал: Создание нового интегрированного терминала**. Когда откроется терминал, выполните команду `func`.

![Проверка предварительных требований для Azure Functions Core Tools.](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Выходные данные, которые начинаются с логотипа Функций Azure (чтобы его увидеть, прокрутите выходные данные вверх), указывают на наличие Azure Functions Core Tools.

Если команда `func` не распознается, убедитесь, что папка, в которую вы установили Azure Functions Core Tools, включена в переменную среды PATH.

> [!div class="nextstepaction"]
> [Вход в Azure выполнен](tutorial-vs-code-serverless-python-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
