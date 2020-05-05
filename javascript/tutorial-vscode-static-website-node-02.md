---
title: Создание статического приложения Node.js с помощью Visual Studio Code
description: Руководство, часть 2. Создание примера приложения
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: 81593d61126b34f002513deb02d9bf634a7e9dd6
ms.sourcegitcommit: 3c69d7c3e5c5a00a01ee18e63b0659830c7d4ec0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105135"
---
# <a name="create-the-app"></a>Создайте приложение

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-static-website-node-01.md)

На этом шаге вы создадите простое приложение, которое можно развернуть в Azure, с помощью интерфейса командной строки (CLI) для [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app), [Vue](https://cli.vuejs.org/) или [Svelte](https://github.com/sveltejs/template). Также можно использовать любую другую платформу JavaScript, которая создает набор статических файлов, или любую папку, содержащую файлы HTML, CSS или JavaScript. Если у вас уже есть приложение, готовое к развертыванию, переходите к [созданию учетной записи хранения Azure](tutorial-vscode-static-website-node-03.md).

# <a name="angular"></a>[Angular](#tab/angular)

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

1. Откройте в браузере страницу по адресу `http://localhost:4200`, чтобы проверить работу приложения:

    ![Работающий пример приложения Angular](media/static-website/local-app-angular.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

# <a name="react"></a>[React](#tab/react)

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

1. Откройте в браузере страницу по адресу `http://localhost:3000`, чтобы проверить работу приложения:

    ![Работающий пример приложения React](media/static-website/local-app-react.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

# <a name="vue"></a>[Vue](#tab/vue)

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

1. Откройте в браузере страницу по адресу `http://localhost:8080`, чтобы проверить работу приложения:

    ![Работающий пример приложения Vue](media/static-website/local-app-vue.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Создайте шаблон нового приложения с именем my-static-app, выполнив в CLI следующую команду:

    ```bash
    npx degit sveltejs/template my-static-app
    ```

1. Затем перейдите к новой папке и выполните команду `npm install`:

    ```bash
    cd my-static-app
    npm install
    ```

1. Теперь выполним сборку приложения с помощью команды `npm run build`:

    ```bash
    npm run build
    ```

1. После этого в папке _public_ должна появиться папка _build_. Эта папка _build_ содержит файлы HTML, CSS и JavaScript для развертывания в службе хранилища Azure.

1. Запустите приложение с помощью следующей команды:

     ```bash
     npm run dev
     ```

1. Откройте в браузере страницу по адресу `http://localhost:5000`, чтобы проверить работу приложения:

    ![Работающий пример приложения Vue](media/static-website/local-app-svelte.png)

1. Чтобы отключить сервер, нажмите клавиши **CTRL**+**C** в окне терминала или в командной строке.

---

> [!div class="nextstepaction"]
> [Приложение создано](tutorial-vscode-static-website-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
