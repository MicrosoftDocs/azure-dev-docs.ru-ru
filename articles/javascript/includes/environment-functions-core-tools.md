---
title: include file completed.md
description: include file completed.md
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 09f69293062f52dfc2190cfeb040ad45ea428c67
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338513"
---
## <a name="install-the-azure-functions-core-tools"></a>Установка основных инструментов Функций Azure

Чтобы включить локальную отладку, необходимо установить [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools). Это можно выполнить прямо в Visual Studio Code.

1. Запустите Visual Studio Code.

1. Откройте **палитру команд** ( **F1** ) и введите **Azure Functions: Install or Update Azure Functions Core Tools** (Функции Azure: установка или обновление Azure Functions Core Tools), а затем нажмите клавишу **ВВОД** , чтобы выполнить команду.

1. Чтобы проверить установку, в VS Code выберите команду меню **Терминал** > **Новый терминал** , а затем выполните команду `func`. После выполнения команды должны отобразиться выходные данные, как показано ниже (вместе со сведениями об использовании).

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