---
title: Создание приложения Node.js для развертывания в Azure с помощью Azure CLI
description: Руководство, часть 2. Создание кода приложения с помощью Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8c8597dc887f6bff3bd6c5a1070e628f7af0cc65
ms.sourcegitcommit: 1dfcc022a3098b1a1505e9458eada35f527ef070
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97658457"
---
# <a name="create-the-app-code-using-express"></a>Создание кода приложения с помощью Express

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-azure-cli-node-01.md)

На этом этапе вы создадите простое приложение Node.js с помощью [Express](https://www.expressjs.com), используя [генератор Express](https://expressjs.com/en/starter/generator.html).

1. Используйте приведенную ниже команду, чтобы запустить генератор Express и сформировать шаблон нового приложения Express с именем myExpressApp. В параметрах `--view pug --git` указано, что генератор должен использовать обработчик шаблонов [pug](https://pugjs.org/api/getting-started.html) (ранее известный под именем Jade) и создать файл с расширением *.gitignore*.

    ```bash
    npx express-generator myExpressApp --view pug –git
    ```

1. Перейдите в папку приложения и установите зависимости приложения, выполнив следующие команды:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Запустите сервер приложений с помощью следующей команды:

    ```bash
    npm start
    ```

1. В браузере перейдите по ссылке `http://localhost:3000`, чтобы просмотреть запущенное приложение.

    ![Локальное выполнение приложения Express](../../media/azure-cli/local-app.png)

1. Завершив тестирование приложения, остановите работу сервера, нажав клавиши **CTRL**+**C** в окне терминала, где выполнялась команда `npm start`.

> [!div class="nextstepaction"]
> [Приложение создано](tutorial-vscode-azure-cli-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=express)