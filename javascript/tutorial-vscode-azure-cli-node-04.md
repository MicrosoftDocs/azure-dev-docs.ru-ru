---
title: Развертывание кода приложения в Службе приложений Azure с помощью Azure CLI
description: Руководство, часть 4. Развертывание веб-сайта
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: fbf8c9d77876e5617aee5ed5b27257a4aaca2f41
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686157"
---
# <a name="deploy-the-app-to-app-service"></a>Развертывание приложения в Службе приложений

[Предыдущий шаг. Создание Службы приложений](tutorial-vscode-azure-cli-node-03.md)

На этом этапе вы развернете код приложения Node.js в Службе приложений Azure, используя базовый процесс передачи локального репозитория Git в Azure.

1. В окне терминала или в командной строки выполните приведенные ниже команды, чтобы инициализировать локальный репозиторий Git и выполнить начальную фиксацию (папка *node_modules* игнорируется, так как она включена в файл *.gitignore*, который вы создали ранее при выполнении экспресс-генератора).

    ```bash
    git init
    git add -A
    git commit -m "Initial Commit"
    ```

1. Выполните приведенные ниже команды, чтобы настроить учетные данные развертывания в Azure, заменив `username` и `pPassword` реальными значениями из учетных данных. При успешном выполнении этой команды отображаются выходные данные в формате JSON.

    ```bash
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. Выполните приведенную ниже команду, чтобы получить конечную точку Git. В нее мы будем отправлять код приложения. При выполнении команды замените значение `<your_app_name>` реальным именем, которое вы указали при создании Службы приложений на предыдущем шаге:

    ```bash
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    Результат выполнения этой команды будет выглядеть примерно так:

    ```output
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    ```

1. Выполните следующую команду, чтобы задать для Git новый удаленный объект с именем `azure`, указав URL-адрес из предыдущего шага *без имени пользователя*. Для примера из предыдущего шага эта команда будет выглядеть так:

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. Выполните следующую команду, чтобы развернуть код приложения из репозитория Git в Службе приложений. Вам будет предложено ввести учетные данные:

    ```bash
    git push azure master
    ```

1. При выполнении команды отображается несколько сообщений от удаленного узла. Когда процесс завершится, обновите в браузере страницу, на которой открыт URL-адрес приложения, чтобы проверить работу кода:

    ![Код приложения, выполняющийся в Azure](media/azure-cli/remote-app.png)

> [!TIP]
> Если поступит сообщение об ошибке `Object #<eventemitter> has no method 'hrtime'`, скорее всего, вам нужно указать на сайте версию среды выполнения. Следующая команда указывает, что сайт должен использовать версию Node `6.9.1`. Если для сайта нужна другая или более поздняя версия Node, используйте полную версию семантической записи `major.minor.patch`.
>
> ```bash
> az webapp config appsettings set --name <your_app_name> --settings
> ```

> [!div class="nextstepaction"]
> [Приложение развернуто](tutorial-vscode-azure-cli-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
