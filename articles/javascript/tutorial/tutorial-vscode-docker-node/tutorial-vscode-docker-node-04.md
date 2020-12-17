---
title: Создание образа контейнера для приложения Node.js из Visual Studio Code
description: Руководство по Docker, часть 4. Создание образа приложения Node.js
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 01a64f74547358efee45bc3b83b68a0b01bfcea8
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609303"
---
# <a name="create-your-nodejs-application-image"></a>Создание образа приложения Node.js

[Предыдущий шаг. Создание и запуск локального приложения Node.js](tutorial-vscode-docker-node-03.md)

## <a name="add-docker-files"></a>Добавление файлов Docker

1. В Visual Studio Code откройте **палитру команд** (клавишей **F1**), введите `add docker files to workspace` и выберите команду **Docker: Add Docker files to workspace** (Docker: добавить файлы Docker в рабочую область).

1. Когда появится запрос, выберите тип приложения **Node.js** и ответьте **Нет** на вопрос о файлах Docker Compose. Затем выберите порт (должен быть номер — 3000) для ожидания передачи данных от приложения.

1. С помощью этой команды создаются `Dockerfile` и несколько файлов конфигурации для Docker Compose, а также `.dockerignore`.

## <a name="build-a-docker-image"></a>Создание образа Docker

В `Dockerfile` описывается среда для приложения, в том числе указывается расположение исходных файлов и приводится команда для запуска приложения в контейнере.

1. Откройте **палитру команд** (с помощью клавиши **F1**) и выполните команду **Docker Images: Build Image** (Образы Docker: создание образа), чтобы создать образ. VS Code использует Dockerfile в текущей папке и присваивает образу то же имя, что и у текущей папки.

1. По завершении в Visual Studio Code откроется панель **Терминал** для выполнения команды `docker build`. В этих выходных данных отображается каждый шаг (слой), который входит в среду приложения.

1. После создания образа он появится в обозревателе **DOCKER** в разделе **Образы**.

    ![Список образов Docker в Visual Studio Code](../../media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Отправка образа в реестр

1. Чтобы отправить образ в реестр, сначала нужно добавить к нему тег с именем реестра. В обозревателе **DOCKER** щелкните правой кнопкой мыши **последний** образ.

    ![Команда добавления тега к образу в Visual Studio Code](../../media/deploy-containers/tag-command.png)

1. Когда появится следующий запрос, завершите добавление тегов и нажмите клавишу **ВВОД**.

    В соответствии с соглашением при добавлении тегов используется следующий формат:

    `[registry or username]/[image name]:[tag]`

    Если вы используете Реестр контейнеров Azure, имя образа будет аналогично следующему:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Если вы работаете с Docker Hub, используйте имя пользователя в Docker Hub. Пример:

    `fiveisprime/myexpressapp:latest`

1. Теперь в узле в разделе **Образы** появится новый образ с тегами, содержащий имя реестра. Разверните этот узел, щелкните правой кнопкой мыши **Последний** и выберите **Отправить**.

    ![Команда отправки образа в Visual Studio Code](../../media/deploy-containers/push-command.png)

1. На панели **Терминал** отображаются команды `docker push`, которые использовались для этой операции. Целевой реестр определяется реестром, указанным в имени образа. 

1. Если в выходных данных отображается сообщение "Требуется проверка подлинности", выполните `az acr login --name <your registry name>` в терминале.

1. По завершении разверните в обозревателе расширений Docker узел **Registries** (Реестры), чтобы найти свой образ в реестре.

    ![Отправленный образ отображается в Реестре контейнеров Azure](../../media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Образ для приложения создан](tutorial-vscode-docker-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
