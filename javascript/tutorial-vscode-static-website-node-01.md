---
title: Развертывание статического веб-сайта Node.js в Azure из Visual Studio Code
description: Руководство, часть 1. Общие сведения и предварительные требования.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 8b19b32933c2405082e2d1e2aa46c7a06af1f9d1
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467088"
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

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Все обязательные компоненты установлены](tutorial-vscode-static-website-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
