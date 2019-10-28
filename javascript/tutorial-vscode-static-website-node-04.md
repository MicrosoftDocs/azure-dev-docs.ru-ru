---
title: Развертывание файлов статического приложения Node.js в службе хранилища Azure из Visual Studio Code
description: Руководство, часть 4. Развертывание файлов в службе хранилища Azure
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: 705256291709c6715f90f19c220a7e3e127f923f
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587163"
---
# <a name="deploy-the-website-to-azure-storage"></a>Развертывание веб-сайта в Службе хранилища Azure

[Предыдущий шаг. Создание учетной записи хранения](tutorial-vscode-static-website-node-03.md)

На этом этапе с помощью Visual Studio Code вы развернете файлы статического веб-сайта, созданные на предыдущих шагах, в службе хранилища Azure, которая отвечает за размещение и обслуживание этих файлов.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Узлы службы хранилища Azure в обозревателе хранилищ Azure](media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _dist/my-static-app_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Команда развертывания статического веб-сайта](media/static-website/deploy-build-angular.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _dist/my-static-app_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Команда развертывания статического веб-сайта](media/static-website/deploy-build-angular.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Сообщение о завершении развертывания](media/static-website/deployment-complete.png)

    ![Статический веб-сайт, работающий в Azure](media/static-website/azure-app-angular.png)

# <a name="reacttabreact"></a>[React](#tab/react)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Узлы службы хранилища Azure в обозревателе хранилищ Azure](media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _build_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Команда развертывания статического веб-сайта](media/static-website/deploy-build-react.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _build_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Команда развертывания статического веб-сайта](media/static-website/deploy-build-react.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Сообщение о завершении развертывания](media/static-website/deployment-complete.png)

    ![Статический веб-сайт, работающий в Azure](media/static-website/azure-app-react.png)

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. В Visual Studio Code перейдите к обозревателю **службы хранилища Azure**, а затем поочередно разверните узел подписки, узел ранее созданной учетной записи хранения Azure и узел **Контейнеры BLOB-объектов**. Код приложения развертывается в контейнере `$web`.

   ![Узлы службы хранилища Azure в обозревателе хранилищ Azure](media/static-website/storage-nodes.png)

1. Выберите обозреватель **Файлы**, щелкните правой кнопкой мыши папку _dist_ и выберите пункт **Deploy to Static Website** (Развернуть статический веб-сайт):

    ![Команда развертывания статического веб-сайта](media/static-website/deploy-build-vue.png)

1. При появлении запроса выберите имя созданной ранее учетной записи хранения.

1. Когда развертывание завершится, появится сообщение с кнопкой **Browse to website** (Перейти к веб-сайту). Нажмите эту кнопку, чтобы открыть основную конечную точку развернутого кода приложения.

    ![Сообщение о завершении развертывания](media/static-website/deployment-complete.png)

    ![Статический веб-сайт, работающий в Azure](media/static-website/azure-app-vue.png)

---

> [!div class="nextstepaction"]
> [Мой сайт работает в Azure](tutorial-vscode-static-website-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
