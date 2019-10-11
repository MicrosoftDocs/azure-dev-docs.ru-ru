---
title: Создание экземпляра Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 2. Создание приложения Node.js
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 769ca61ef7716eaa08d45e109a9bf50e5801721c
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686185"
---
# <a name="create-your-nodejs-application"></a>Создание приложения Node.js

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-azure-app-service-node-01.md)

На этом этапе вы создадите простое приложение Node.js с помощью генератора приложений Express, которое затем сможете развернуть в Azure.

Вы также можете использовать приложение, описанное в [руководстве по работе с Node.js в Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial). В таком случае можно сразу перейти к статье о [развертывании приложения](tutorial-vscode-azure-app-service-node-03.md).

1. В окне терминала или командной строке выполните приведенную ниже команду, чтобы запустить генератор Express и сформировать шаблон нового приложения Express с именем myExpressApp. В параметрах `--view pug --git` указано, что генератор должен использовать обработчик шаблонов [pug](https://pugjs.org/api/getting-started.html) (ранее известный под именем Jade) и создать файл с расширением *.gitignore*.

    ```bash
    npx express-generator myExpressApp --view pug –git
    ```

1. Установите зависимости приложения, выполнив `npm install` в папке приложения:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Запустите сервер, выполнив команду `npm start`.

    ```bash
    npm start
    ```

1. Протестируйте приложение, перейдя по ссылке [http://localhost:3000](http://localhost:3000) в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение приложения Express](media/deploy-azure/express.png)

> [!div class="nextstepaction"]
> [Приложение Node.js создано](tutorial-vscode-azure-app-service-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
