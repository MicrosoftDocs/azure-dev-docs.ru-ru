---
title: Создание приложения Node.js в Службе приложений Azure с помощью Visual Studio Code
description: Руководство по Node.js, часть 2. Создание приложения Node.js и его запуск в локальной среде
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7d627d97987ed41f90972362ab38d436a6157d33
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365247"
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
