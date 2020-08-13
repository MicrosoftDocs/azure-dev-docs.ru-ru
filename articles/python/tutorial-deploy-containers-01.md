---
title: Руководство по Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code
description: 'Руководство, шаг 1: общие сведения и предварительные требования.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: fe98ff2007be9f817cc8e9bd0210761579eef536
ms.sourcegitcommit: 5051b25ad32be891800b23fc7ae12a4ca85cbb73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88147415"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Руководство по Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code

В этой статье описано, как в [Службе приложений Azure](/azure/app-service/) развернуть образ контейнера из реестра контейнеров с помощью Visual Studio Code.

Если на любом из шагов этого руководства возникнут проблемы, сообщите нам об этом. Используйте ссылку **У меня есть проблема** в конце каждой статьи, чтобы отправить отзыв.

Демонстрационное видео с виртуальной конференции PyCon 2020: <a href="https://www.youtube.com/watch?v=t79HDLC5kQA&feature=youtu.be&ocid=AID3006292" target="_blank">Приложения Django в контейнерах разработки VS Code</a> (YouTube.com).

## <a name="prerequisites"></a>Предварительные требования

- [учетная запись Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension);
- [Visual Studio Code](https://code.visualstudio.com/)
- Подходящий контейнер, который был отправлен в реестр контейнеров. Сведения о создании контейнера с веб-приложением Python см. в статье [Python в контейнерах](https://code.visualstudio.com/docs/containers/quickstart-python).
- [Расширение Службы приложений Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- [Расширение Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Вход в Azure выполнен — перейти к шагу 2 >>>](tutorial-deploy-containers-02.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=01-verify-prerequisites)
