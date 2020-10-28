---
title: include file tutorial-azure-web-app-mongodb-04.md
description: include file tutorial-azure-web-app-mongodb-04.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 73e629427359a625ea3ec4796c2ec7000a21536a
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183942"
---
В этом разделе руководства показано, как добавить к приложению локальную базу данных MongoDB с помощью расширения Visual Studio Code и контейнеров Docker.

## <a name="configure-visual-studio-code-to-run-containers"></a>Настройка Visual Studio Code для запуска контейнеров

В этом разделе показано, как настроить среду разработки для запуска двух контейнеров: одного для проекта Node.js и другого для контейнера MongoDB. В этом разделе используются контейнеры разработки Visual Studio Code, поэтому конфигурация контейнера сохраняется в папке **.devcontainer** . Вы можете зафиксировать ее в системе управления версиями, чтобы ваши коллеги также получили доступ к локальному развертыванию MongoDB.  

1. В Visual Studio Code откройте палитру команд (сочетанием клавиш CTRL+SHIFT+P) и выберите действие **Remote-Containers: Add Development Container Configuration Files** (Удаленные контейнеры. Добавление файлов конфигурации для контейнера разработки). 

1. Выберите в списке вариант **Node.js & Mongo DB** .

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-configure-development-container.png" alt-text="Снимок части экрана с палитрой команд Visual Studio Code."::: 

1. В файле **\.devcontainer\devcontainer.json** найдите свойство **forwardPorts** , раскомментируйте его и добавьте в массив `8080`. Если вам требуется получить доступ к контейнеру MongoDB с помощью оболочки, добавьте в массив порт `27017`.  

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-dev-container-configuration-forward-ports.png" alt-text="Снимок части экрана с палитрой команд Visual Studio Code."::: 

## <a name="run-web-app-locally-with-database"></a>Запуск веб-приложения в локальной среде с базой данных

В этом разделе показано, как запустить среду разработки с обоими контейнерами и открыть веб-сайт для просмотра. 

1. Щелкните зеленый значок **Remote Containers** (Удаленные контейнеры) в левом нижнем углу Visual Studio Code. Откроется палитра команд. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-remote-container-icon.png" alt-text="Снимок части экрана с палитрой команд Visual Studio Code."::: 

1. На палитре команд выберите **Remote-Containers: Reopen in Container** (Удаленные контейнеры. Повторно открыть в контейнере). При первом открытии проекта с контейнерами будут извлечены и запущены образы Node.js и MongoDB. Это может занять несколько минут. 

    Когда выполняются контейнеры, терминал Visual Studio Code отображает терминал контейнера Node.js. 

    Вы можете выполнить команду `ls` для просмотра файлов, если потребуется. Обратите внимание, что файлы размещаются в общем томе, который используется вашим локальным компьютером. Изменения, вносимые в файлы кода в контейнере Node.js, сохраняются в локальных файлах.

1. Запустите проект, выполнив следующую команду в терминале:

    ```console
    npm start
    ```

1. Откройте URL-адрес локального веб-приложения в любом браузере:

    ```
    http://localhost:8080/
    ```

1. Введите данные в поля формы и отправьте ее. Результаты отобразятся подсистемой отрисовки React, выполняемой на стороне сервера. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Снимок части экрана с палитрой команд Visual Studio Code.":::

1. Завершив изучение приложения, остановите контейнеры с помощью палитры команд, выбрав действие **Remote-Containers: Reopen Locally...** (Удаленные контейнеры. Открыть локально...). Это действие останавливает контейнеры, но сохраняет их на локальном компьютере. 

## <a name="want-to-know-more"></a>Хотите получить дополнительные сведения? 

Подключитесь к контейнеру MongoDB с помощью расширения Visual Studio Code ( **[MongoDB для VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)** ), чтобы просмотреть данные.

Если вы предпочитаете использовать оболочку **mongo** , подключитесь к контейнеру MongoDB из терминала Visual Studio Code, открыв новое окно Visual Studio Code, а затем выполните команду **Remote-Containers: Attach to Running Container...** (Удаленные контейнеры. Присоединить к выполняющемуся контейнеру...) и выберите контейнер, имя которого заканчивается на `-db`. После присоединения окна к контейнеру откройте окно терминала Visual Studio Code. Вы можете сразу войти в оболочку Mongo с помощью следующей команды:

```console
mongo
```

Когда вы будете готовы удалить контейнеры, используйте расширение **[Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)** для Visual Studio Code.