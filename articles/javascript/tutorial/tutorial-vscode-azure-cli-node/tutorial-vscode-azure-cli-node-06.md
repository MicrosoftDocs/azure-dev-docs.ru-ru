---
title: Внесение изменений в код приложения и его повторное развертывание в Azure
description: Руководство, часть 6. Внесение изменений и повторное развертывание с помощью Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1c80c76759d22195fc7268d4b072a7a25c3a1745
ms.sourcegitcommit: 75a1f26aaff48a89631805df4b4a0c006de6a271
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128179"
---
# <a name="part-6-make-changes-and-redeploy"></a>Часть 6. Внесение изменений и повторное развертывание

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-azure-cli-node-05.md)

На этом этапе вы внесете изменения в код приложения, зафиксируете их в локальном репозитории Git, а затем повторно развернете сайт, отправив в Azure.

1. В папке `myExpressApp` откройте файл *src/server.js* и измените сообщение `Welcome to Azure!`.

    ![Изменение файла src/server.js](../../media/azure-cli/edit-server-file.png)

1. Сохраните файл.

1. В окне терминала или командной строке зафиксируйте изменения в Git, выполнив следующую команду:

    ```bash
    git commit -a -m "Edited message"
    ```

1. Отправьте изменения в удаленный репозиторий Git с именем Azure, созданный ранее:

    ```bash
    git push azure <DEFAULT-BRANCH-NAME>
    ```

1. Так как Служба приложений подключена к репозиторию Git, в выходных данных команды показано, что изменения автоматически публикуются в Azure: 

    ```output
    Enumerating objects: 7, done.
    Counting objects: 100% (7/7), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 405 bytes | 202.00 KiB/s, done.
    Total 4 (delta 2), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id '9fd89a25b6'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling node.js deployment.
    remote: Creating app_offline.htm
    remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
    remote: Copying file: 'src\server.js'
    remote: Deleting app_offline.htm
    remote: Using start-up script bin/www from package.json.
    remote: Generated web.config.
    remote: The package.json file does not specify node.js engine version constraints.
    remote: The node.js application will run with the 
    remote: ..
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
       a98bad8..9fd89a2  master -> master
    ```

1. Чтобы увидеть эти изменения, обновите приложение в браузере:

    ![Опубликованные изменения, которые отображаются в браузере](../../media/azure-cli/remote-app-changes.png)

> [!div class="nextstepaction"]
> [Изменения отображаются](tutorial-vscode-azure-cli-node-07.md) [Возникла ошибка](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=publishing-changes)
