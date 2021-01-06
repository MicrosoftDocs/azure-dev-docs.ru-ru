---
title: Введение и предварительные требования
description: Локальное создание и развертывание клиентского приложения React или TypeScript в виде статического веб-приложения Azure с помощью действия GitHub.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: dfd5803fe79a0a000173d2d4e3fe52b3c4f029c5
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687448"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Создание и развертывание статического веб-приложения в Azure

В этом руководстве описано локальное создание и развертывание клиентского приложения React или TypeScript в виде статического веб-приложения Azure с помощью действия GitHub. Приложение React позволяет анализировать изображения с помощью Компьютерного зрения Cognitive Services.

* [**Образец кода**](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="prerequisites"></a>Предварительные требования

- Установите на локальный компьютер [Node.js и npm](https://nodejs.org/en/download).
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
    - [Статические веб-приложения Azure (предварительная версия)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) для развертывания приложения React в статическое веб-приложение Azure.
- Средство [Git](https://git-scm.com/downloads) для отправки данных в GitHub, по которым активируется действие GitHub.
- [Учетная запись GitHub](https://github.com/join) для создания вилки репозитория и отправки данных.
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) можно использовать в среде Bash.

   [![Внедрение запуска](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)   
- При необходимости [установите](/cli/azure/install-azure-cli) Azure CLI, чтобы выполнять справочные команды CLI.
   - Если вы используете локальную установку, выполните вход с помощью команды Azure CLI [az login](/cli/azure/reference-index#az-login).  Чтобы выполнить аутентификацию, следуйте инструкциям в окне терминала.  Сведения о дополнительных возможностях, доступных при входе, см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).
  - Если появится запрос, установите расширения Azure CLI при первом использовании.  Дополнительные сведения о расширениях см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Выполните команду [az version](/cli/azure/reference-index?#az_version), чтобы узнать установленную версию и зависимые библиотеки. Чтобы обновиться до последней версии, выполните команду [az upgrade](/cli/azure/reference-index?#az_upgrade).

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Сведения об архитектуре клиентского приложения и процессе развертывания с помощью действия GitHub](./application-architecture.md) 
