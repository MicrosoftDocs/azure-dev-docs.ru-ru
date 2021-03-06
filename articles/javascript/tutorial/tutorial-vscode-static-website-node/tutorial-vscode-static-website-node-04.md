---
title: Развертывание файлов статического приложения Node.js в службе хранилища Azure из Visual Studio Code
description: Руководство по статическим веб-приложениям, часть 4. Развертывание файлов в службе хранилища Azure
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 0c24f8e22f68a4616cbe6e9cd998264158972c19
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609436"
---
# <a name="deploy-the-website-to-azure-storage"></a>Развертывание веб-сайта в Службе хранилища Azure

[Предыдущий шаг. Создание учетной записи хранения](tutorial-vscode-static-website-node-03.md)

На этом этапе с помощью Visual Studio Code вы развернете файлы статического веб-сайта, созданные на предыдущих шагах, в службе хранилища Azure, которая отвечает за размещение и обслуживание этих файлов.

# <a name="angular"></a>[Angular](#tab/angular)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Узлы службы хранилища Azure в обозревателе хранилищ Azure](../../media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _dist/my-static-app_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Angular — команда развертывания статического веб-сайта](../../media/static-website/deploy-build-angular.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Angular — сообщение о завершении развертывания](../../media/static-website/deployment-complete.png)

    ![Angular — статический веб-сайт, работающий в Azure](../../media/static-website/azure-app-angular.png)

# <a name="react"></a>[React](#tab/react)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![React — узлы службы хранилища Azure в обозревателе хранилищ Azure](../../media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _build_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![React — команда развертывания статического веб-сайта](../../media/static-website/deploy-build-react.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![React — сообщение о завершении развертывания](../../media/static-website/deployment-complete.png)

    ![React — статический веб-сайт, работающий в Azure](../../media/static-website/azure-app-react.png)

# <a name="vue"></a>[Vue](#tab/vue)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Vue — узлы службы хранилища Azure в обозревателе хранилищ Azure](../../media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _dist_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Vue — команда развертывания статического веб-сайта](../../media/static-website/deploy-build-vue.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Vue — сообщение о завершении развертывания](../../media/static-website/deployment-complete.png)

    ![Vue — статический веб-сайт, работающий в Azure](../../media/static-website/azure-app-vue.png)

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Svelte — узлы службы хранилища Azure в обозревателе хранилищ Azure](../../media/static-website/storage-nodes.png)

1. Откройте **проводник**, щелкните правой кнопкой мыши папку _public_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Svelte — команда развертывания статического веб-сайта](../../media/static-website/deploy-build-svelte.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Svelte — сообщение о завершении развертывания](../../media/static-website/deployment-complete-svelte.png)

    ![Svelte — статический веб-сайт, работающий в Azure](../../media/static-website/azure-app-svelte.png)

---

> [!div class="nextstepaction"]
> [Мой сайт работает в Azure](tutorial-vscode-static-website-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
