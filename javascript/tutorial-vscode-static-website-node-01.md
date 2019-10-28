---
title: Развертывание статического веб-сайта Node.js в Azure из Visual Studio Code
description: Руководство, часть 1. Общие сведения и предварительные требования.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 0e5a7e12d234b56899e3c814cb577002125ea052
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587134"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Развертывание статического веб-сайта в Azure из Visual Studio Code

При работе с этим руководством вы создадите и развернете статический веб-сайт в Azure с помощью [службы хранилища Azure](https://docs.microsoft.com/azure/storage). Статический веб-сайт состоит из файлов HTML, CSS, JavaScript и некоторых других, таких как файлы изображений или шрифтов. Обычно статический сайт — это одностраничное приложение ([SPA](https://en.wikipedia.org/wiki/Single-page_application)). Он создается с помощью Angular, React или Vue. Независимо от метода разработки, файлы размещаются и обслуживаются непосредственно в _хранилище_ (без специального веб-сервера). Размещение в хранилище проще и дешевле, чем обслуживание веб-сервера.

> [!NOTE]
> Если у вас есть собственный серверный код, например приложение Node.js/Express, вместо этого руководства перейдите к [руководству по Службе приложений](tutorial-vscode-azure-app-service-node-01.md).

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение службы хранилища Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
- [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов для Node.js). Этот пункт используется только для создания примера проекта. Если код приложения уже готов, Node.js устанавливать не обязательно.

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurestorage">Установка расширения службы хранилища Azure </a>

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Все обязательные компоненты установлены](tutorial-vscode-static-website-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
