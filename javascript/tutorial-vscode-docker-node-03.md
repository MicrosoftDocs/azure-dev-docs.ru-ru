---
title: Создание образа контейнера для приложения Node.js из Visual Studio Code
description: Руководство, часть 3. Создание образа приложения Node.js
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 1b79f84bd69853578796b4485ca669be98f41006
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686099"
---
# <a name="create-your-nodejs-application-image"></a>Создание образа приложения Node.js

[Предыдущий шаг. Использование реестра контейнеров](tutorial-vscode-docker-node-02.md)

На этом этапе с помощью расширения DOCKER в Visual Studio Code вы добавите необходимые файлы для создания образа приложения, создадите образ и отправите его в реестр.

Если у вас еще нет приложения для этого пошагового руководства, используйте приложение из [руководства по Node.js в Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="add-docker-files"></a>Добавление файлов Docker

1. В Visual Studio Code откройте **палитру команд** (клавишей **F1**), введите `add docker files to workspace` и выберите команду **Docker: Add Docker files to workspace** (Docker: добавить файлы Docker в рабочую область).

1. Выберите тип приложения **Node.js**, когда появится соответствующий запрос. Затем выберите порт для ожидания передачи данных от приложения.

1. С помощью этой команды создаются `Dockerfile` и несколько файлов конфигурации для Docker Compose, а также `.dockerignore`.

    > [!TIP]
    > VS Code хорошо работает с файлами Docker. Дополнительные сведения о расширенных функциях языка, таких как интеллектуальные предложения, завершения и обнаружение ошибок, см. в разделе [о работе с Docker](https://code.visualstudio.com/docs/azure/docker) в документации по VS Code.

## <a name="build-a-docker-image"></a>Создание образа Docker

В `Dockerfile` описывается среда для приложения, в том числе указывается расположение исходных файлов и приводится команда для запуска приложения в контейнере.

> [!TIP]
> Сравнение контейнеров и образов: Контейнер представляет собой экземпляр образа.

1. Откройте **палитру команд** (с помощью клавиши **F1**) и выполните команду **Docker Images: Build Image** (Образы Docker: создание образа), чтобы создать образ.

1. При появлении запроса выберите только что созданный `Dockerfile`, а затем присвойте образу имя. Это имя должно включать целевой реестр или учетную запись Docker Hub:

    `[registry or username]/[image name]:[tag]`

    В рамках этого руководства мы используем Реестр контейнеров Azure. Поэтому имя образа выглядит так:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Если вы работаете с Docker Hub, используйте имя пользователя в Docker Hub. Например:

    `fiveisprime/myexpressapp:latest`

1. По завершении в Visual Studio Code откроется панель **Терминал**для выполнения команды `docker build`. В этих выходных данных отображается каждый шаг (слой), который входит в среду приложения.

1. После создания образа он появится в обозревателе **DOCKER** в разделе **Образы**.

    ![Список образов Docker в Visual Studio Code](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Отправка образа в реестр

1. В Visual Studio Code откройте **палитру команд** (с помощью клавиши **F1**) и выполните команду **Docker Images: Push** (Образы Docker: отправка), выбрав только что созданный образ. На панели**Терминал** отображаются команды `docker push`, которые использовались для этой операции.

1. По завершении разверните узел **Образы** в обозревателе расширений Docker, чтобы найти свой образ.

    ![Отправленный образ отображается в Реестре контейнеров Azure](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Образ для приложения создан](tutorial-vscode-docker-node-04.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
