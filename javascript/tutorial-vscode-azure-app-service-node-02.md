---
title: Создание экземпляра Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 2. Создание приложения Node.js и его запуск в локальной среде
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: 86d3801b31f1a0c5fb988940a7c9f550a991f0d2
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82105195"
---
# <a name="create-and-run-a-local-nodejs-app"></a>Создание и запуск локального приложения Node.js

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-azure-app-service-node-01.md)

На этом этапе вы создадите простое приложение Node.js с помощью генератора приложений Express. Затем вы запустите приложение локально.

1. В окне терминала или командной строки перейдите в расположение, в котором нужно создать папку приложения.

1. Выполните приведенную ниже команду, чтобы с помощью генератора Express создать приложение Express с именем *expressApp1*. В параметрах `--view pug --git` указано, что генератор должен использовать обработчик шаблонов [pug](https://pugjs.org/api/getting-started.html) (ранее известный под именем Jade) и создать файл с расширением *.gitignore*.

    ```bash
    npx express-generator expressApp1 --view pug -–git
    ```

1. Перейдите в папку приложения:

    ```bash
    cd expressApp1
    ```

1. Установите зависимости приложения:

    ```bash
    npm install
    ```

1. Запустите сервер:

    ```bash
    npm start
    ```

1. Протестируйте приложение, перейдя по ссылке `http://localhost:3000` в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение приложения Express](media/deploy-azure/express.png)

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.

> [!div class="nextstepaction"]
> [Приложение Node.js создано](tutorial-vscode-azure-app-service-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
