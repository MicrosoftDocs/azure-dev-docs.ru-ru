---
title: Развертывание Функций Azure в Node.js с помощью Visual Studio Code
description: Руководство, часть 1. Общие сведения и предварительные требования.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 915ab0cb07fe6f4cf74d812c022d5e0e9d266177
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80439156"
---
# <a name="deploy-azure-functions-from-visual-studio-code"></a>Развертывание Функций Azure с помощью Visual Studio

В этом руководстве объясняется, как с помощью Visual Studio Code и расширения "Функции Azure" создать и развернуть приложение в решении "Функции Azure", написанное на JavaScript.

## <a name="walkthrough-video"></a>Пошаговое видеоруководство

Просмотрите это видео, чтобы ознакомиться с полным пошаговым руководством по содержимому этой статьи.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-Azure-Functions-from-Visual-Studio-Code/player]

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение "Функции Azure"](vscode:extension/ms-azuretools.vscode-azurefunctions).
- [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов для Node.js).

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurefunctions">Установка расширения "Функции Azure"</a>

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="install-the-azure-functions-core-tools"></a>Установка основных инструментов Функций Azure

Чтобы включить локальную отладку, необходимо установить [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools). Это можно выполнить прямо в Visual Studio Code.

1. Запустите Visual Studio Code.

1. Откройте **палитру команд** (**F1**) и введите **Azure Functions: Install or Update Azure Functions Core Tools** (Функции Azure: установка или обновление Azure Functions Core Tools), а затем нажмите клавишу **ВВОД**, чтобы выполнить команду.

1. Чтобы проверить установку, в VS Code выберите команду меню **Терминал** > **Новый терминал**, а затем выполните команду `func`. После выполнения команды должны отобразиться выходные данные, как показано ниже (вместе со сведениями об использовании).

    <pre>
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %

    Azure Functions Core Tools (2.4.419 Commit hash: c9c1724d002bd90b2e6b41393915ea3a26bcf0ce)
    Function Runtime Version: 2.0.12332.0
    </pre>

> [!div class="nextstepaction"]
> [Все обязательные компоненты установлены](tutorial-vscode-serverless-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=getting-started)
