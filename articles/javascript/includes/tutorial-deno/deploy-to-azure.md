---
title: Включить файл 3
description: Включить файл 3
ms.topic: tutorial
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: d186468b865ddf221c5232d5312018dd3d3858af
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278694"
---
На этом шаге вы развернете приложение Deno в Azure с помощью Azure CLI.

1. Чтобы создать группу ресурсов с именем `deno-quickstart`, выполните следующую команду:

    ```bash
    az groups create -n deno-quickstart -l eastus
    ```

    Если вы решите изменить имя группы ресурсов, обязательно обновите все флаги `-g` в следующих шагах.

1. Создайте план Службы приложений с именем `deno-plan`, который будет содержать ваш веб-сайт, с помощью следующей команды:

    ```bash
    az appservice plan create -g deno-quickstart -n deno-plan --is-linux
    ```

1. Далее вы создадите само веб-приложение. Эта команда создаст экземпляр Службы приложений и свяжет ее с ранее созданным планом. Для тега `<your-app-name>` укажите значение имени, которое вы хотите присвоить веб-приложению. Оно должно быть уникальным.

    ```bash
    az webapp create -n <your-app-name> -g deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    В этом экземпляре Службы приложений выполняется образ Docker, который предоставляет базовую функциональность для выполнения любого кода Deno. Для завершения этого процесса может понадобиться несколько секунд.

1. Когда создание завершится, настройте некоторые переменные. Это можно сделать, выполнив следующую команду:

    ```bash
    az webapp config container set -n <your-app-name> -g deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file '' && \
    az webapp config appsettings set -n <your-app-name> -g deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

Теперь Служба приложений настроена и готова получить приложение, созданное на предыдущем шаге. Но для запуска приложение нужно упаковать в пакет `.zip`. Для этого выполните следующие шаги.

1. Перейдите к папке `deno-demo`.

    ```bash
    cd deno-demo
    ```

1. Выполните команду `zip`.

    ```bash
    zip demo demo.ts
    ```

    Эта команда создаст файл с именем `demo.zip` в той же папке, где находится файл `demo.ts`.

1. После упаковки можно передать файл в Службу приложений для выполнения:

    ```bash
    az webapp deployment source config-zip -n <your-app-name> -g deno-quickstart --src ./demo.zip && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. Протестируйте приложение, перейдя по адресу `https://<your-app-name>.azurewebsites.net`.