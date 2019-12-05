---
title: Внесение изменений в код приложения и его повторное развертывание в Azure
description: Руководство, часть 6. Внесение изменений и повторное развертывание
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 9702f10795893004965631fa99dfbfab181f2292
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467158"
---
# <a name="make-changes-and-redeploy"></a>Внесение изменений и повторное развертывание

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-azure-cli-node-05.md)

На этом этапе вы внесете изменения в код приложения, зафиксируете их в локальном репозитории Git, а затем повторно развернете сайт, отправив в Azure.

1. В папке `myExpressApp` откройте файл *views/index.pug* и замените сообщение в строке 5 текстом `p Welcome to Azure!`.

    ![Изменение файла index.pug](media/azure-cli/editpugfile.png)

1. Сохраните файл.

1. В окне терминала или командной строке зафиксируйте изменения в Git, выполнив следующую команду:

    ```bash
    git commit -a -m "Edited message"
    ```

1. Отправьте изменения в удаленный репозиторий Git с именем Azure, созданный ранее:

    ```bash
    git push azure master
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
    remote: Copying file: 'views\index.pug'
    remote: Deleting app_offline.htm
    remote: Using start-up script bin/www from package.json.
    remote: Generated web.config.
    remote: The package.json file does not specify node.js engine version constraints.
    remote: The node.js application will run with the default node.js version 6.9.5.
    remote: Selected npm version 3.10.10
    remote: ..
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
       a98bad8..9fd89a2  master -> master
    ```

1. Чтобы увидеть эти изменения, обновите приложение в браузере:

    ![Опубликованные изменения, которые отображаются в браузере](media/azure-cli/remote-app-changes.png)

> [!div class="nextstepaction"]
> [Изменения отображаются](tutorial-vscode-azure-cli-node-07.md) [Возникла ошибка](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=publishing-changes)
