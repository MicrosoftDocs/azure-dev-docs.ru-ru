---
title: Руководство по Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code
description: 'Руководство, шаг 1: общие сведения и предварительные требования.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 60189f960087688c68876935ba3407811bbec7c6
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422442"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Руководство по Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code

В этой статье описано, как в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/containers/) развернуть образ контейнера из реестра контейнеров с помощью Visual Studio Code.

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте ссылку **У меня есть проблема** в конце каждой статьи, чтобы отправить отзыв.

## <a name="prerequisites"></a>Предварительные требования

- [учетная запись Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension);
- [Visual Studio Code](https://code.visualstudio.com/)
- Подходящий контейнер, который был отправлен в реестр контейнеров. Например, см. сведения о [создании контейнера с веб-приложением Python и его добавлении в реестр](https://code.visualstudio.com/docs/python/tutorial-create-containers).
- [Расширение Службы приложений Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- [Расширение Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-deploy-containers-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=01-verify-prerequisites)
