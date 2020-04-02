---
title: Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code
description: 'Шаг руководства 1: введение, предварительные требования и вход в Azure.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 8995c31203b2cbd096820832beb3d6a7d165f132
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441969"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Руководство по Развертывание приложений Python в Службе приложений Azure в Linux с помощью Visual Studio Code

В этой статье описано, как развернуть приложение Python в Службе приложений Azure в Linux с использованием Visual Studio Code и расширения [Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте ссылку **У меня есть проблема** в конце каждой статьи, чтобы отправить отзыв.

> [!TIP]
> [Служба приложений Azure в Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) предназначена для выполнения исходного кода в предварительно определенном контейнере Docker. Этот контейнер запускает приложения в среде Python 3.7, используя веб-сервер [Gunicorn](https://gunicorn.org). Характеристики этого контейнера описаны в статье [Настройка приложения Python в Linux для Службы приложений Azure](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python). Само определение контейнера находится по адресу [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code с расширением Azure Dev Spaces](#visual-studio-code-python-and-the-azure-app-service-extension).
- Среда Python

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python и расширение Azure App Service

Установите следующее программное обеспечение:

- [Visual Studio Code](https://code.visualstudio.com/).
- Python и [расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), как описано в разделе [предварительные требования](https://code.visualstudio.com/docs/python/python-tutorial) руководства по работе с Python в VS Code.
- [Расширение Службы приложений Azure](vscode:extension/ms-azuretools.vscode-azureappservice), которое организует взаимодействие со Службой приложений Azure из VS Code. Для получения общих сведений ознакомьтесь с [руководством](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) по расширению Службы приложений и посетите репозиторий [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) на сайте GitHub.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-deploy-app-service-on-linux-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=01-verify-prerequisites)
