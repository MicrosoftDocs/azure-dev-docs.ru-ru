---
title: Создание статического приложения Node.js с помощью Visual Studio Code
description: Руководство, часть 2. Создание примера приложения
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 566d166a69bbbee59726b8e381bee4a24077d8c6
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685981"
---
# <a name="create-the-app"></a>Создание приложения

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-static-website-node-01.md)

На этом этапе вы создадите простое приложение для развертывания в Azure с помощью CLI служебной программы React, [create-react-app](https://github.com/facebook/create-react-app). Кроме того, можно использовать Angular, Vue или любую другую платформу либо папку с HTML-файлами. Если у вас уже есть приложение, готовое к развертыванию, переходите к [созданию учетной записи хранения Azure](tutorial-vscode-static-website-node-03.md).

1. Создайте с помощью средства create-react-app новое приложение React с именем `my-react-app` с помощью следующей команды:

    ```bash
    npm create react-app my-react-app
    ```

1. Скомпилируйте приложение. Для этого перейдите в новую папку и выполните команду `npm run build`:

    ```bash
    cd my-react-app
    npm run build
    ```

1. Это действие создаст папку *build* в папке *my-react-app*. Эта папка *build* содержит файлы HTML, CSS и JavaScript для развертывания в службе хранилища Azure.

1. Запустите приложение с помощью следующей команды:

    ```bash
    npm start
    ```

1. Откройте в браузере страницу по адресу [http://localhost:3000](http://localhost:3000), чтобы проверить работу приложения:

    ![Работающий пример приложения React](media/static-website/local-app.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

> [!div class="nextstepaction"]
> [Приложение создано](tutorial-vscode-static-website-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
