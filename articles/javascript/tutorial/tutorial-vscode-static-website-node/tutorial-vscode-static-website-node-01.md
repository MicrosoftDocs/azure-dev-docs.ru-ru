---
title: Развертывание статического веб-сайта Node.js в Azure из Visual Studio Code
description: Руководство по статическим веб-приложениям, часть 1. Общие сведения и предварительные требования.
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: a4280e3c28ee99e38eb4430c991450667277b29f
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609312"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Развертывание статического веб-сайта в Azure из Visual Studio Code

При работе с этим руководством вы создадите и развернете статический веб-сайт в Azure с помощью [службы хранилища Azure](/azure/storage). Статический веб-сайт состоит из файлов HTML, CSS, JavaScript и некоторых других, таких как файлы изображений или шрифтов. Обычно статический сайт — это одностраничное приложение ([SPA](https://en.wikipedia.org/wiki/Single-page_application)). Он создается с помощью Angular, React или Vue. Независимо от метода разработки, файлы размещаются и обслуживаются непосредственно в _хранилище_ (без специального веб-сервера). Размещение в хранилище проще и дешевле, чем обслуживание веб-сервера.

## <a name="walkthrough-video"></a>Пошаговое видеоруководство

Просмотрите это видео, чтобы ознакомиться с полным пошаговым руководством по содержимому этой статьи.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player]

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение службы хранилища Azure.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)
- [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов для Node.js). Этот пункт используется только для создания примера проекта. Если код приложения уже готов, Node.js устанавливать не обязательно.

### <a name="azure-subscription"></a>Подписка Azure.

Если у вас нет подписки Azure, [зарегистрируйтесь прямо сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) и получите бесплатную учетную запись с кредитами Azure на сумму 200 долл. США, которые позволят проверить любое сочетание служб.

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](../../includes/azure-sign-in.md)]

> [!div class="nextstepaction"] 
> [Все обязательные компоненты установлены](tutorial-vscode-static-website-node-02.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
