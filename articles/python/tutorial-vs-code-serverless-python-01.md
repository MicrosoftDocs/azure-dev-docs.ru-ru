---
title: Руководство по Создание и развертывание бессерверных функций Azure на Python с помощью VS Code
description: Шаг 1 учебника. Настройка локальной среды для бессерверных Функций Azure.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperf-fy21q2
ms.openlocfilehash: 2baff5675850de5c8af750efeb49cdec31e5190b
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911424"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Руководство по Создание и развертывание бессерверных функций Azure на языке Python с помощью Visual Studio Code

Из этой статьи вы узнаете, как с помощью Visual Studio Code и расширения Функций Azure создать бессерверную конечную точку HTTP с поддержкой Python, а также как добавить подключение (привязку) к хранилищу. Расширение "Функции Azure" для Visual Studio Code значительно упрощает процесс использования функций, автоматически обрабатывая многие вопросы конфигурации.

Бессерверная среда Функций Azure предполагает, что Azure предоставляет конечные точки и общедоступные URL-адреса приложения без необходимости подготавливать виртуальную машину, публиковать веб-приложение или иным образом управлять серверами и ресурсами. Azure эффективно управляет всеми этими ресурсами, что значительно сокращает расходы на размещение приложения. Дополнительные сведения см. в статье [Общие сведения о Функциях Azure](/azure/azure-functions/functions-overview).

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте кнопку обратной связи **Эта страница**, которую можно найти в конце каждой статьи.

Демонстрационное видео с виртуальной конференции PyCon 2020: <a href="https://www.youtube.com/watch?v=9bMsdBYy-D0&feature=youtu.be&ocid=AID3006292" target="_blank">Создание Функций Azure с помощью VS Code</a> (YouTube.com). Вам также может быть интересен более длинный доклад: <a href="https://www.youtube.com/watch?v=PV7iy6FPjAY&feature=youtu.be&t=13&ocid=AID3006292" target="_blank">Простая обработка данных с помощью Функций Azure</a> (YouTube.com).

## <a name="configure-your-environment"></a>Настройка среды

- Если у вас нет учетной записи Azure с активной подпиской, [создайте учетную запись бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension).

- Выполните инструкции в следующих разделах:

  - [Установка основных инструментов Функций Azure.](#azure-functions-core-tools)

  - [Установка Python и Visual Studio Code с расширением "Функции Azure".](#visual-studio-code-python-and-the-azure-functions-extension)

  - [Вход в Azure](#sign-in-to-azure)

  - [Проверка окружения](#verify-your-environment)
 
### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Следуйте инструкциям по [работе с Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2) для вашей операционной системы. Игнорируйте комментарии в статье о диспетчере пакетов Chocolately. Они не нужны для работы с этим руководством.

При установке Node.js используйте параметры по умолчанию и *не* выбирайте параметр для автоматической установки необходимых средств.  Кроме того, обязательно используйте параметр `-g` с командами `npm install`, чтобы средства Core Tools были доступны для последующих команд.

> [!TIP]
> Средства Core Tools написаны на .NET Core, а пакет Core Tools лучше всего устанавливать с помощью диспетчера пакетов npm из набора Node.js, а значит, вам пока нужно устанавливать .NET Core и Node.js даже для работы с Функциями Azure на Python. Но вы можете обойтись без .NET Core, используя "пакеты расширений", как описано в упомянутой выше документации. В любом случае все эти компоненты нужно устанавливать только один раз, после чего Visual Studio Code будет автоматически предлагать вам все нужные обновления.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python и расширение "Функции Azure"

Установите следующее программное обеспечение:

- 64-разрядная версия Python 3.6, 3.7 или 3.8 для работы с Функциями Azure. Установите Python с сайта [python.org](https://www.python.org/downloads). При установке выберите **Add Python 3.x to PATH** (Добавить в путь Python 3.x). Используйте параметры по умолчанию, выбрав **Установить сейчас**. В Windows также выберите **Disable Path length limit** (Отключить ограничение длины пути) в конце процесса.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительных требований](https://code.visualstudio.com/docs/python/python-tutorial) учебника по Python в VS Code.
- [Расширение "Функции Azure"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Общие сведения см. в репозитории [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions) на сайте GitHub.

    > [!NOTE]
    > Расширение "Функции Azure" входит в состав [пакета расширений Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

### <a name="sign-in-to-azure"></a>Вход в Azure

Установив расширение Azure, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure**, в разделе **Функции** выберите **Войти в Azure** и следуйте инструкциям в браузере.

![Вход в Azure с помощью VS Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

После входа убедитесь, что в строке состояния указано **Azure: вход выполнен** и ваши подписки отображаются в обозревателе **Azure**.

![Строка состояния Visual Studio Code, в которой отображается учетная запись Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

![Обозреватель Службы приложений Azure Visual Studio Code с отображаемой подпиской](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

[!INCLUDE [proxy-config](includes/proxy-config.md)]

### <a name="verify-your-environment"></a>Проверка окружения

Чтобы убедиться, что набор средств Azure Functions Core Tools установлен, откройте палитру команд Visual Studio Code (клавишей **F1**) и выберите команду **Терминал: Создание нового интегрированного терминала**. Когда откроется терминал, выполните команду `func`.

![Проверка предварительных требований для Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

Выходные данные, которые начинаются с логотипа Функций Azure (чтобы его увидеть, прокрутите выходные данные вверх), указывают на наличие Azure Functions Core Tools.

Если команда `func` не распознана, снова выполните команду `npm install -g azure-functions-core-tools` и убедитесь, что установка прошла успешно. Кроме того, обязательно используйте параметр `-g` с командой install, иначе npm установит пакет только в текущей папке.

Команда `func` работает с файлом *func. cmd*, установленным в глобальной папке Node.js. Чтобы узнать расположение этой папки, выполните команду `npm -l` и просмотрите соответствующие сведения в конце выходных данных.

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-vs-code-serverless-python-02.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
