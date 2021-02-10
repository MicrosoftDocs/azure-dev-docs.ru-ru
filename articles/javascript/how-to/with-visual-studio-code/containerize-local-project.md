---
title: Создание контейнера из локального проекта
description: Создание Dockerfile для локального проекта разработки с помощью Visual Studio Code
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: a299e4647e3257627b62242a4ab63b16fc5590a3
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230183"
---
# <a name="create-a-container-image-from-your-local-javascript-project"></a>Создание образа контейнера из локального проекта JavaScript

Выполнение приложения в контейнере позволяет развернуть единообразный интерфейс для пользователей веб-приложения. Поскольку Docker является довольно сложным инструментом, Visual Studio Code предоставляет расширение, которое упрощает выполнение типичных задач Docker.

## <a name="prepare-your-environment"></a>Подготовка среды 

[Docker](https://www.docker.com/) должен быть установлен и запущен. Убедитесь в этом, выполнив следующую команду:

```bash
docker system info
```

Если Docker не установлен или не работает, эта команда возвратит ошибку. 

## <a name="create-a-container"></a>Создание контейнера

1. Откройте Visual Studio в существующем проекте JavaScript. 
1. На панели действий нажмите значок **Расширения**, затем найдите `docker`и выберите **расширение** [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).
1. Установите расширение Docker, а затем перезагрузите Visual Studio Code.

    ![Установка расширения Docker для Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-docker-extension.png)

    Расширение Docker для Visual Studio Code включает команду для создания *Dockerfile* и файла *docker-compose.yml* для имеющегося проекта.

1. Щелкните значок Docker на панели действий, а затем просмотрите контейнеры Docker на боковой панели.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-activity-bar-side-bar.png" alt-text="Экран &quot;Поиск по фразе &quot;git branch&quot; и выбор пункта &quot;Git: Create Branch&quot;.":::

## <a name="view-available-docker-commands"></a>Просмотр доступных команд Docker

Чтобы просмотреть доступные команды Docker, откройте палитру команд. Для этого нажмите клавишу **F1** и введите `docker`.

![Команды, которые поддерживает расширение Docker для Visual Studio Code ](../../media/node-howto-e2e/visual-studio-code-available-docker-codes.png)

## <a name="create-a-dockerfile-in-your-project"></a>Создание Dockerfile в проекте

1. Если вы используете систему управления версиями, например **git**, убедитесь в отсутствии других изменений. Это поможет просмотреть созданные для вас файлы.

1. Выберите **Docker: Docker: Add docker files to workspace** (Docker: добавление файлов docker в рабочую область) и параметры для проекта. 

    Эти параметры являются общими для проекта Node.js:

    |Параметр|Значение|
    |--|--|
    |Платформа приложений|Node.js|
    |Package.json|package.json|
    |Предоставляемый порт|8080 — или автоматически определенное значение|
    |Добавить дополнительные файлы docker (.dockerignore) |да|

    ![Созданный файл Dockerfile в Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-complete-dockerfile.png)

    Команда Docker создает полный файл *Dockerfile* и файлы docker-compose, которые вы можете сразу использовать.

## <a name="build-image-from-your-project"></a>Создание образа из проекта

1. Нажмите клавишу **F1**, введите `dockerb` на палитре команд, а затем щелкните **Docker: сборка образа**. 
1. Выберите файл *Dockerfile*, который вы только что создали. 
1. Если у файла package.json есть свойство name, оно будет использоваться в качестве имени образа контейнера. 
    Если у вас нет файла package.json, укажите тег в формате `ALIAS/IMAGE-NAME` где ALIAS — это псевдоним Docker, а IMAGE-NAME — имя образа проекта. Например, `diberry/express-web-app`. 
1. Нажмите клавишу **ВВОД**, чтобы запустить встроенное окно терминала, в котором отображаются выходные данные создаваемого образа Docker.

    ![Выходные данные сборки образа Docker](../../media/node-howto-e2e/docker-build-image-output.png)

    Команда автоматически запускает `docker build`.

1. Щелкните значок Docker на панели действий, а затем выберите **Образы**, чтобы просмотреть новый образ в списке образов. 
    
    Вы также можете заметить, что в списке появились другие новые образы. Docker извлекает образ, на котором основан контейнер.  

## <a name="run-local-container-project"></a>Выполнение проекта локального контейнера

1. Щелкните значок Docker на панели действий.
1. Щелкните правой кнопкой мыши имя образа из списка **Образы** и выберите **Запустить**.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-running-container-visual-studio-code.png" alt-text="Экран &quot;Выбор правой кнопкой мыши имени образа из списка &quot;Образы&quot; и выполнение команды &quot;Запустить&quot;":::

## <a name="push-local-container-image-to-dockerhub"></a>Отправка образа локального контейнера в DockerHub

Чтобы создать веб-приложение Azure из образа, необходимо, чтобы образ был доступен из реестра. Образ может быть общедоступным в реестре сообщества или находится в частном реестре, например [Реестре контейнеров Azure](/azure/container-registry/), доступ к которому можно получить, пройдя проверку подлинности. 

Чтобы сделать это, войдите в DockerHub, запустив `docker login` из интерфейса командной строки и введя учетные данные своей учетной записи.

1. Откройте палитру команд в Visual Studio Code, нажав клавишу F1.
1. Введите `dockerpush`, а затем выберите команду `Docker: Push`. 
1. Выберите только что созданный тег изображения (например, `diberry/express-web-app`) и нажмите клавишу **ВВОД**. 
1. Команда автоматизирует вызов `docker push` и отображает выходные данные в окне встроенного терминала.

## <a name="push-local-container-image-to-azure-container-registry"></a>Передача образа локального контейнера в Реестр контейнеров Azure

Ознакомьтесь с инструкциями по [проверке подлинности и принудительной отправке в собственный Реестр контейнеров Azure](../with-azure-cli/create-container-registry-resource.md).

## <a name="next-steps"></a>Следующие шаги

* [Создание ресурса Реестра контейнеров Azure](../with-azure-cli/create-container-registry-resource.md)