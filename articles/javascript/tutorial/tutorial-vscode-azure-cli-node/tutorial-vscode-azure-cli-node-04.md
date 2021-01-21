---
title: Развертывание кода приложения в Службе приложений Azure с помощью Azure CLI
description: Руководство, часть 4. Развертывание веб-сайта с помощью Azure CLI
ms.topic: tutorial
ms.date: 12/14/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 368597fb10397f7e7ba8feb82b29d031c292dab0
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561010"
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

1. Выполните приведенную ниже команду, чтобы [настроить учетные данные развертывания на уровне пользователя, с помощью Azure CLI](/azure/app-service/deploy-configure-credentials), заменив `username` и `password` новыми учетными данными, которые относятся исключительно к этому развертыванию. Эти учетные данные не совпадают с учетными данными подписки Azure. 

    ```azurecli
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. Выполните приведенную ниже команду, чтобы [получить конечную точку Git, с помощью Azure CLI](/cli/azure/webapp/deployment/source). В эту конечную точку мы будем отправлять код приложения. При выполнении команды замените значение `<your_app_name>` реальным именем, которое вы указали при создании Службы приложений на предыдущем шаге:

    ```azurecli
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    Результат выполнения этой команды будет выглядеть примерно так:

    <pre>
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    </pre>

1. Выполните следующую команду, чтобы задать для Git новый удаленный объект с именем `azure`, указав URL-адрес из предыдущего шага *без имени пользователя*. Для примера из предыдущего шага эта команда будет выглядеть так:

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. Выполните следующую команду, чтобы развернуть код приложения из репозитория Git в Службе приложений. Вам будет предложено ввести учетные данные:

    ```bash
    git push azure <DEFAULT-BRANCH-NAME>
    ```

1. При выполнении команды отображается несколько сообщений от удаленного узла. Когда процесс завершится, обновите в браузере страницу, на которой открыт URL-адрес приложения, чтобы проверить работу кода:

    ![Код приложения, выполняющийся в Azure](../../media/azure-cli/remote-app.png)

> [!div class="nextstepaction"]
> [Приложение развернуто](tutorial-vscode-azure-cli-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
