---
title: Руководство по Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code
description: 'Руководство, шаг 1: общие сведения и предварительные требования.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: f6cdd345fddf0123cb26549ddbc498f156737799
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172300"
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
> [Вход в Azure выполнен](tutorial-deploy-containers-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=01-verify-prerequisites)
