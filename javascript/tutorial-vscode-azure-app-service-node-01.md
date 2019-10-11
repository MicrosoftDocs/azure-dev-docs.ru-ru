---
title: Развертывание приложений Node.js в Службе приложений Azure с помощью Visual Studio Code
description: Руководство, часть 1. Общие сведения и предварительные требования.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 9c9c7085788f39e82eaec78b11d9c679b20c24eb
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686221"
---
# <a name="deploy-to-azure-app-service-using-visual-studio-code"></a>Развертывание в Службе приложений Azure с помощью Visual Studio Code

В этом руководстве объясняется, как развернуть приложение Node.js в Службе приложений Azure в Linux с помощью расширения [Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Службы приложений Azure](vscode:extension/ms-azuretools.vscode-azureappservice).
- [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов для Node.js).

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azureappservice">Установка расширения Службы приложений Azure</a>

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Расширение Azure установлено](tutorial-vscode-azure-app-service-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=getting-started)
