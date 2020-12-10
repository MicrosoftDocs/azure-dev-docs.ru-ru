---
title: Развертывание приложений Node.js в Службе приложений Azure с помощью Azure CLI
description: Руководство, часть 1. Общие сведения и предварительные требования для работы с Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6e16b4baacf845648f021d1bb7cbbd9080e29804
ms.sourcegitcommit: 09b4a2dbe13601fdf16fcc4082a5075b46ad3459
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559288"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Развертывание в Службе приложений Azure с помощью Azure CLI

При работе с этим руководством вы развернете приложение Node.js в Службе приложений Azure с помощью [интерфейса командной строки (CLI) Azure](/cli/azure/overview?view=azure-cli-latest), который работает во всех операционных системах. С помощью этого CLI вы можете создавать ресурсы Azure, настраивать конвейер развертывания из репозитория Git в Azure и просматривать выходные данные приложения `console.log`.

## <a name="prerequisites"></a>предварительные требования

- [Подписка Azure](#azure-subscription).
- [Node.js и npm 6.x или более поздней версии](https://nodejs.org/en/download) (диспетчер пакетов для Node.js).
- [Git](https://git-scm.com/downloads), для которого с помощью команды `git --version` отображается номер версии.
- [Установите Azure CLI](/cli/azure/install-azure-cli) или используйте [Azure Cloud Shell](https://shell.azure.com).

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="sign-in-to-azure-with-azure-cli"></a>Вход в Azure с помощью Azure CLI

[!INCLUDE [Sign in ](../azure-cli/includes/interactive-login.md)]

## <a name="check-npm-version"></a>Проверка версии npm

Если у вас уже установлена платформа Node.js, выполните команду `node -v` и убедитесь, что используется платформа версии 10.x или более поздней версии. Если у вас более старая версия, [обновите](https://nodejs.org/en/download/) ее до самого свежего выпуска LTS (выпуска с долгосрочной поддержкой).

> [!div class="nextstepaction"]
> [Все обязательные компоненты установлены](tutorial-vscode-azure-cli-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)
