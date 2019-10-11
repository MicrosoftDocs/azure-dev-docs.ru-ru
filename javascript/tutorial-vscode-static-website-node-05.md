---
title: Развертывание изменений для статического веб-сайта Node.js с помощью Visual Studio Code
description: Руководство, часть 5. Внесение изменений и повторное развертывание
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 986d2a0f8999d79dfd1d856ed20a053c495a3765
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685936"
---
# <a name="make-changes-and-redeploy"></a>Внесение изменений и повторное развертывание

[Предыдущий шаг. Развертывание в службе хранилища Azure](tutorial-vscode-static-website-node-04.md)

На этом этапе вы внесете простое изменение в исходный код приложения и повторно развернете сайт, чтобы выполнить полный рабочий процесс развертывания.

1. В Visual Studio Code откройте файл *src/app.js* и измените строку 11, чтобы она соответствовала следующему примеру:

    ```js
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. В окне терминала или в командной строке выполните команду `npm run build`.

1. В VS Code щелкните правой кнопкой мыши обновленную папку *build* и снова выберите **Deploy to Static Website** (Развернуть статический веб-сайт). Выберите учетную запись хранения и подтвердите, что хотите развернуть изменения. (Перед развертыванием изменений расширение Azure автоматически удаляет старые файлы, чтобы избежать проблем с кэшированием.)

1. Когда развертывание завершится, обновите сайт в браузере, чтобы увидеть изменения:

    ![Изменения в приложении после повторного развертывания](media/static-website/updated-azure-app.png)

> [!div class="nextstepaction"]
> [Изменения развернуты](tutorial-vscode-static-website-node-06.md) [Возникла ошибка](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
