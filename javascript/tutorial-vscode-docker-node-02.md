---
title: Использование реестра контейнера в Visual Studio Code
description: Руководство, часть 2. Использование реестра контейнеров
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 01e35f12e83a7e53d6d5b78c4fcf156d9a5b53f0
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82026177"
---
# <a name="use-a-container-registry"></a>Использование Реестра контейнеров

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-docker-node-01.md)

На этом этапе вы настроите подходящий реестр контейнеров для образа приложения. Службы размещения с поддержкой контейнеров, например Служба приложений Azure, могут извлекать образы из реестра.

При работе с этим руководством используется [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/), который предоставляет частный защищенный и размещенный реестр для образов. Но описанные здесь процессы подходят и для других реестров, например [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure.

1. В Visual Studio Code нажмите клавишу **F1**, чтобы открыть палитру команд.

1. В поле поиска введите **реестр**. В списке результатов выберите **Реестр контейнеров Azure: создать реестр**.

   ![Обозреватель Docker в Visual Studio Code](media/deploy-containers/docker-create-registry.jpg)

1. Введите или выберите следующие значения:

    - В поле **Имя реестра** введите имя, уникальное в пределах Azure и содержащее от 5 до 50 буквенно-цифровых символов.
    - В поле **SKU** выберите **Базовый**.
    - В поле **Группа ресурсов** введите значение, которое является уникальным в пределах подписки.
    - В поле **Расположение** выберите ближайший к вам регион.

    Visual Studio Code начнет процесс создания реестра в Azure. По завершении вы увидите уведомление, аналогичное приведенному ниже. Оно подтверждает, что реестр был успешно создан.

   ![Подтверждение в Visual Studio Code о создании реестра](media/deploy-containers/registry-created.jpg)

1. Откройте обозреватель **Docker** и убедитесь, что в разделе **Реестры** отображается только что настроенная конечная точка реестра.

   ![Проверка отображения реестра в обозревателе Docker](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Вход в Реестр контейнеров Azure

Хотя вы можете видеть свои реестры Azure в расширении Docker, вы не сможете отправлять в них образы, пока не войдете в Реестр контейнеров.

1. В Visual Studio нажмите клавиши **CTRL**+ **`** , чтобы открыть встроенный терминал.

1. Выполните следующую команду Azure CLI, чтобы войти в Реестр контейнеров. Замените `<your-registry-name>` именем созданного реестра.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Реестр создан](tutorial-vscode-docker-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
 