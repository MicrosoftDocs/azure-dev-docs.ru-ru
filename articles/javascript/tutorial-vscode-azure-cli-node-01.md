---
title: Развертывание приложений Node.js в Службе приложений Azure с помощью Azure CLI
description: Руководство, часть 1. Общие сведения и предварительные требования для работы с Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 4ca11ffff1889d69ef8a1ee7fbca991b714f2163
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365207"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Развертывание в Службе приложений Azure с помощью Azure CLI

При работе с этим руководством вы развернете приложение Node.js в Службе приложений Azure с помощью [интерфейса командной строки (CLI) Azure](/cli/azure/overview?view=azure-cli-latest), который работает во всех операционных системах. С помощью этого CLI вы можете создавать ресурсы Azure, настраивать конвейер развертывания из репозитория Git в Azure и просматривать выходные данные приложения `console.log`.

## <a name="prerequisites"></a>предварительные требования

- [Подписка Azure](#azure-subscription).
- [Node.js и npm 6.x или более поздней версии](https://nodejs.org/en/download) (диспетчер пакетов для Node.js).
- [Git](https://git-scm.com/downloads), для которого с помощью команды `git --version` отображается номер версии.
- [Интерфейс командной строки Azure](/cli/azure/install-azure-cli).

Кроме того, вы можете использовать [расширение Azure CLI для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Она поддерживает раскраску кода, дополнение ввода IntelliSense и фрагменты кода при работе со скриптами Azure CLI.

Вторая альтернатива — [Azure Cloud Shell](/azure/cloud-shell/overview). Эту службу можно использовать из Visual Studio Code с помощью [расширения для учетных записей Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="sign-in-to-the-azure-cli"></a>Вход в Azure CLI

После установки Azure CLI выполните следующую команду в окне терминала или командной строки:

```azurecli
az login
```

Эта команда позволяет открыть окно браузера, в котором вам будет предложено войти в Azure. После успешного входа в окне терминала отобразятся выходные данные в формате JSON со сведениями о подписке.

## <a name="check-npm-version"></a>Проверка версии npm

Если у вас уже установлена платформа Node.js, выполните команду `node -v` и убедитесь, что используется платформа версии 10.x или более поздней версии. Если у вас более старая версия, [обновите](https://nodejs.org/en/download/) ее до самого свежего выпуска LTS (выпуска с долгосрочной поддержкой).

> [!div class="nextstepaction"]
> [Все обязательные компоненты установлены](tutorial-vscode-azure-cli-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)