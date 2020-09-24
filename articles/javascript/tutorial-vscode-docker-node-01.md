---
title: Развертывание контейнеров Docker в Службе приложений Azure с помощью Visual Studio Code
description: Руководство, часть 1. Общие сведения и предварительные требования.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: df92b27e88b750956b7e46cc944cf627e077f1dd
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "91110499"
---
# <a name="deploy-containers-to-azure-app-service"></a>Развертывание веб-приложения в Службу приложений Azure

В этом руководстве объясняется, как с помощью Visual Studio Code создать контейнерное приложение Node.js, используя Docker, отправить образ контейнера в реестр, а затем развернуть образ в Службе приложений Azure.

## <a name="walkthrough-video"></a>Пошаговое видеоруководство

Просмотрите это видео, чтобы ознакомиться с полным пошаговым руководством по содержимому этой статьи.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-containers-Azure-App-Service/player]

## <a name="prerequisites"></a>предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).
- [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов для Node.js).
- [Docker](https://www.docker.com/community-edition).

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker">Установка расширения Docker</a>

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice">Установка расширения Службы приложений Azure</a>

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="verify-docker-install"></a>Проверка установки Docker

Убедитесь, что расширение Docker установлено правильно, выполнив следующую команду в окне терминала или в командной строке:

```bash
docker --version
```

Выходные данные должны быть примерно следующими:

<pre>
Docker Version 17.12.0-ce, build c97c6d6
</pre>

> [!div class="nextstepaction"]
> [Расширение Docker установлено](tutorial-vscode-docker-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=getting-started)
