---
title: Развертывание образа контейнера для приложения Node.js в Visual Studio Code
description: Руководство, часть 4. Развертывание образа в Службе приложений Azure
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 60eb5be0b3d4049c7955195f3bb6bc85dd2b2498
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686019"
---
# <a name="deploy-the-image-to-azure-app-service"></a>Развертывание образа в Службе приложений Azure

[Предыдущий шаг. Создание образа приложения](tutorial-vscode-docker-node-03.md)

На этом этапе вы развернете в [Службе приложений Azure](https://azure.microsoft.com/en-us/services/app-service/) образ, который ранее отправили в реестр, непосредственно из Visual Studio Code.

1. В обозревателе **DOCKER** разверните узлы вашего образа в разделе **Реестры**, щелкните правой кнопкой мыши `:latest` и выберите пункт **Deploy Image to Azure App Service** (Развернуть образ в Службе приложений Azure).

    ![Развертывание из обозревателя](media/deploy-containers/deploy-image-command.png)

1. Предоставьте значения для Службы приложений, когда появится соответствующий запрос.

    - Это имя должно быть уникальным в пределах Azure.
    - Выберите существующую группу ресурсов или создайте новую. (По сути, **группа ресурсов** — это именованная коллекция всех ресурсов приложения в Azure.)
    - Выберите существующий план службы приложений или создайте новый. (**План Службы приложений** определяет физические ресурсы, в которых размещается веб-сайт. Для работы с этим руководством можно использовать уровень "Базовый" или "Бесплатный".)

1. Когда развертывание завершится, в Visual Studio Code отобразится уведомление с URL-адресом веб-сайта:

    ![Сообщение об успешном развертывании](media/deploy-containers/deploy-successful.png)

1. Вы можете просмотреть эти результаты на панели **выходных данных** в Visual Studio Code, в разделе **Docker**:

    ![Выходные данные успешного развертывания](media/deploy-containers/deploy-output.png)

1. Чтобы открыть развернутый веб-сайт, используйте комбинацию **CTRL**+**Щелчок** для URL-адреса на панели **Выходные данные**. Новая Служба приложений также отображается в разделе **Служба приложений** обозревателя **Azure** в Visual Studio Code. В этом разделе вы можете щелкнуть веб-сайт правой кнопкой мыши и выбрать **Обзор веб-сайта**.

> [!div class="nextstepaction"]
> [Мой сайт работает в Azure](tutorial-vscode-docker-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=deploy-app)
