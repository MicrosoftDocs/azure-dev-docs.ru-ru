---
title: Использование реестра контейнера в Visual Studio Code
description: Руководство, часть 2. Использование реестра контейнеров
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e6dde135a2e6482284488fb83d9f811b02249c4d
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740548"
---
# <a name="use-a-container-registry"></a>Использование Реестра контейнеров

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-vscode-docker-node-01.md)

На этом этапе вы настроите подходящий реестр контейнеров для образа приложения. Службы размещения с поддержкой контейнеров, например Служба приложений Azure, извлекают образы из реестра.

В рамках этого руководства мы используем службу [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/) (ACR), которая предоставляет частный, защищенный и размещенный реестр для образов. Но представленные здесь процессы подходят и для других реестров, например [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

1. В Visual Studio Code нажмите клавишу <kbd>F1</kbd>, чтобы открыть **палитру команд**.

1. В поле поиска введите "реестр" и выберите **Реестр контейнеров Azure: создать реестр**.

   ![Обозреватель Docker в VS Code](media/deploy-containers/docker-create-registry.jpg)

1. Предоставьте следующие значения в ответ на соответствующие запросы:

    - **Имя реестра** должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов.
    - Выберите вариант **Базовый** в поле **Номер SKU**.
    - **Группа ресурсов** должна быть уникальной в пределах подписки.
    - В поле **Расположение** выберите близкий к вашему местонахождению регион.

    Visual Studio Code начнет процесс создания реестра в Azure. По завершении вы увидите следующее уведомление с подтверждением того, что реестр был создан.

   ![Подтверждение в Visual Studio Code о создании реестра](media/deploy-containers/registry-created.jpg)

1. Откройте обозреватель **Docker** и убедитесь, что в разделе **Реестры** отображается только что настроенная конечная точка реестра.

   ![Проверка отображения реестра в обозревателе Docker](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Вход в Реестр контейнеров Azure

Хотя вы можете видеть свои реестры Azure в расширении Docker, вы не сможете отправлять в них образы, пока не войдете в Реестр контейнеров Azure (ACR).

1. Нажмите <kbd>Ctrl+`</kbd>, чтобы открыть **встроенный терминал** в VS Code.

1. Выполните следующую команду Azure CLI, чтобы войти в ACR. Замените <your-registry-name> именем только что созданного реестра.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Реестр создан](tutorial-vscode-docker-node-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
