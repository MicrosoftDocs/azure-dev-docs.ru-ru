---
title: Развертывание изменений для статического веб-сайта Node.js с помощью Visual Studio Code
description: Руководство, часть 5. Внесение изменений и повторное развертывание
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: 73d27549e5ff1acb294f8e65fa610d0b46f56914
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587268"
---
# <a name="make-changes-and-redeploy"></a>Внесение изменений и повторное развертывание

[Предыдущий шаг. Развертывание в службе хранилища Azure](tutorial-vscode-static-website-node-04.md)

На этом этапе вы внесете простое изменение в исходный код приложения и повторно развернете сайт, чтобы выполнить полный рабочий процесс развертывания.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. В Visual Studio Code откройте файл _src/app/app.component.html_ и измените строку 305, чтобы она соответствовала следующему примеру:

    ```html
    <span>Welcome To Azure</span>
    ```

1. В окне терминала или в командной строке выполните команду `npm run build`.

1. В VS Code щелкните правой кнопкой мыши обновленную папку _dist/my-static-site_ и снова выберите **Deploy to Static Website** (Развернуть статический веб-сайт). Выберите учетную запись хранения и подтвердите, что хотите развернуть изменения. (Перед развертыванием изменений расширение Azure автоматически удаляет старые файлы, чтобы избежать проблем с кэшированием.)

1. Когда развертывание завершится, обновите сайт в браузере, чтобы увидеть изменения:

    ![Изменения в приложении после повторного развертывания](media/static-website/updated-azure-app-angular.png)

# <a name="reacttabreact"></a>[React](#tab/react)

1. В Visual Studio Code откройте файл _src/app.js_ и измените строку 11, чтобы она соответствовала следующему примеру:

    ```html
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. В окне терминала или в командной строке выполните команду `npm run build`.

1. В VS Code щелкните правой кнопкой мыши обновленную папку _build_ и снова выберите **Deploy to Static Website** (Развернуть статический веб-сайт). Выберите учетную запись хранения и подтвердите, что хотите развернуть изменения. (Перед развертыванием изменений расширение Azure автоматически удаляет старые файлы, чтобы избежать проблем с кэшированием.)

1. Когда развертывание завершится, обновите сайт в браузере, чтобы увидеть изменения:

    ![Изменения в приложении после повторного развертывания](media/static-website/updated-azure-app-react.png)

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. В Visual Studio Code откройте файл _src/App.vue_ и измените строку 11, чтобы она соответствовала следующему примеру:

    ```html
    <HelloWorld msg="Welcome to Azure!" />
    ```

1. В окне терминала или в командной строке выполните команду `npm run build`.

1. В VS Code щелкните правой кнопкой мыши обновленную папку _dist_ и снова выберите **Deploy to Static Website** (Развернуть статический веб-сайт). Выберите учетную запись хранения и подтвердите, что хотите развернуть изменения. (Перед развертыванием изменений расширение Azure автоматически удаляет старые файлы, чтобы избежать проблем с кэшированием.)

1. Когда развертывание завершится, обновите сайт в браузере, чтобы увидеть изменения:

    ![Изменения в приложении после повторного развертывания](media/static-website/updated-azure-app-vue.png)

---

> [!div class="nextstepaction"]
> [Изменения развернуты](tutorial-vscode-static-website-node-06.md) [Возникла ошибка](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
