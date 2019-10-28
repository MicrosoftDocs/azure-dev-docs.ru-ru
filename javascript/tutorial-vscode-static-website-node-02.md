---
title: Создание статического приложения Node.js с помощью Visual Studio Code
description: Руководство, часть 2. Создание примера приложения
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: b57789ca26851c27a3a68c9d095f327ea64164cf
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587237"
---
# <a name="create-the-app"></a>Создание приложения

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-static-website-node-01.md)

На этом шаге вы создадите простое приложение, которое можно развернуть в Azure, с помощью интерфейса командной строки (CLI) для [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app) или [Vue](https://cli.vuejs.org/). Также можно использовать любую другую платформу JavaScript, которая создает набор статических файлов, или любую папку, содержащую файлы HTML, CSS или JavaScript. Если у вас уже есть приложение, готовое к развертыванию, переходите к [созданию учетной записи хранения Azure](tutorial-vscode-static-website-node-03.md).

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. Создайте шаблон нового приложения с именем my-static-app, выполнив в CLI следующую команду:

    ```bash
    npx @angular/cli new my-static-app
    ```

    При появлении в CLI любых вопросов, касающихся конфигурации, нажимайте клавишу ВВОД для выбора параметров по умолчанию.

1. Скомпилируйте приложение. Для этого перейдите в новую папку и выполните команду `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Это действие создаст папку _dist_ в папке _my-static-app_. Внутри папки _dist_ есть папка с тем же именем, что и у вашего проекта, — _my-static-app_. Папка _build/my-static-app_ содержит файлы HTML, CSS и JavaScript для развертывания в службе хранилища Azure.

1. Запустите приложение с помощью следующей команды:

    ```bash
    npm start
    ```

1. Откройте в браузере страницу по адресу [http://localhost:3000](http://localhost:3000), чтобы проверить работу приложения:

    ![Работающий пример приложения Angular](media/static-website/local-app-angular.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

# <a name="reacttabreact"></a>[React](#tab/react)

1. Создайте шаблон нового приложения с именем my-static-app, выполнив в CLI следующую команду:

    ```bash
    npx create-react-app my-static-app
    ```

1. Скомпилируйте приложение. Для этого перейдите в новую папку и выполните команду `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Это действие создаст папку _build_ в папке _my-static-app_. Эта папка _build_ содержит файлы HTML, CSS и JavaScript для развертывания в службе хранилища Azure.

1. Запустите приложение с помощью следующей команды:

    ```bash
    npm start
    ```

1. Откройте в браузере страницу по адресу [http://localhost:3000](http://localhost:3000), чтобы проверить работу приложения:

    ![Работающий пример приложения React](media/static-website/local-app-react.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. Создайте шаблон нового приложения с именем my-static-app, выполнив в CLI следующую команду:

    ```bash
    npx @vue/cli create my-static-app
    ```

При появлении в CLI любых вопросов, касающихся конфигурации, нажимайте клавишу ВВОД для выбора параметров по умолчанию.

1. Скомпилируйте приложение. Для этого перейдите в новую папку и выполните команду `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Это действие создаст папку _dist_ в папке _my-static-app_. Папка _dist_ содержит файлы HTML, CSS и JavaScript для развертывания в службе хранилища Azure.

1. Запустите приложение с помощью следующей команды:

     ```bash
     npm run serve
     ```

1. Откройте в браузере страницу по адресу [http://localhost:8080](http://localhost:8080), чтобы проверить работу приложения:

    ![Работающий пример приложения Vue](media/static-website/local-app-vue.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

---

> [!div class="nextstepaction"]
> [Приложение создано](tutorial-vscode-static-website-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
