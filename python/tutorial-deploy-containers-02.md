---
title: Развертывание образа контейнера в Службе приложений Azure с помощью Visual Studio Code
description: Шаг руководства 2. Развертывание реального образа Docker в Службе приложений Azure из реестра контейнеров
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 27cc6e68892821170c1e438378f8635bd320afe5
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020092"
---
# <a name="deploy-the-image-to-azure"></a>Развертывание образа в Azure

[Предыдущий шаг: предварительные требования](tutorial-deploy-containers-01.md)

Когда образ контейнера будет помещен в реестр, вы сможете легко настроить Службу приложений Azure, в которой выполняется контейнер, с помощью расширения Docker для VS Code.

1. В обозревателе **Docker** разверните раздел **Registries** (Реестры), затем узел нужного реестра (например, **Azure**) и в нем узел с именем образа, где вы найдете образ с тегом `:latest`.

    ![Поиск образа в обозревателе Docker](media/deploy-containers/deploy-find-image.png)

1. Щелкните тег правой кнопкой мыши и выберите **Deploy Image to Azure App Service** (Развернуть образ в Службе приложений Azure).

    ![Выбор команды развертывания в меню](media/deploy-containers/deploy-menu.png)

1. Следуйте инструкциям на экране, чтобы выбрать подписку Azure, выбрать или указать группу ресурсов, указать регион, настроить план службы приложений (самый бюджетный из них — B1) и указать имя сайта. Этот процесс демонстрируется в следующей анимации.

    ![Анимация: создание и развертывание](media/deploy-containers/deploy-to-app-service.gif)

    **Группа ресурсов** — это именованная коллекция разных ресурсов, которые составляют приложение. Включив все ресурсы приложения в одну группу, вы сможете легко управлять этими ресурсами как единым целым. (См. сведения об [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) в документации по Azure.)

    **План службы приложений** определяет физические ресурсы (базовую виртуальную машину), в которых размещается запущенный контейнер. Для этого руководства используется план B1 — самый бюджетный план из тех, которые поддерживают контейнеры Docker. (См. сведения в [описании плана службы приложений](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) в документации по Azure.)

    Имя Службы приложений должно быть уникальным для всей платформы Azure, поэтому обычно оно включает название компании или личное имя. Для рабочих сайтов Служба приложений обычно настраивается с отдельно зарегистрированным доменным именем.

1. Создание Службы приложений занимает несколько минут, а за выполнением этого процесса можно следить на панели вывода VS Code.

1. По завершении **необходимо** добавить в Службу приложений параметр с именем `WEBSITES_PORT`, который будет обозначать прослушиваемый контейнером порт. (Например, если вы используете образ из [руководства по созданию контейнера Python в VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container), это будет порт 5000 для Flask и 8000 для Django.) Чтобы настроить `WEBSITES_PORT`, переключитесь в обозреватель **Azure: Служба приложений**, разверните узел новой Службы приложений (если он еще не появился, обновите отображение), щелкните правой кнопкой мыши **Параметры приложения** и выберите **Добавить новый параметр**. Когда будет предложено, введите имя ключа `WEBSITES_PORT` и значение, соответствующее номеру порта.

    ![Команда для добавления нового параметра в контекстном меню Службы приложений](media/deploy-containers/add-app-service-setting.png)

1. Служба приложений автоматически перезапускается при изменении параметров. Можно также в любое время щелкнуть Службу приложений правой кнопкой мыши и выбрать действие **Перезапустить**.

1. После перезапуска службы откройте в браузере адрес `http://<name>.azurewebsites.net`. Можно также щелкнуть URL-адрес на панели вывода левой кнопкой мыши при нажатой клавише **CTRL** (**CMD** в macOS) или щелкнуть правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выбрать команду **Просмотреть веб-сайт**.

> [!div class="nextstepaction"]
> [Образ развернут](tutorial-deploy-containers-03.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=02-deploy-container)