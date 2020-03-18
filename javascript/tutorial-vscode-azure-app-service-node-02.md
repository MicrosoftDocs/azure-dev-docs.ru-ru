---
title: Создание экземпляра Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 2. Создание приложения Node.js и его запуск в локальной среде
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: b2935554fb337d5f5db1a71e016638869f9d1dc4
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893643"
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

1. Протестируйте приложение, перейдя по ссылке [http://localhost:3000](http://localhost:3000) в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение приложения Express](media/deploy-azure/express.png)

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.

> [!div class="nextstepaction"]
> [Приложение Node.js создано](tutorial-vscode-azure-app-service-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
