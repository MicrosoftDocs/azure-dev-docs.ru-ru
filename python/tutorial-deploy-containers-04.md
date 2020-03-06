---
title: Шаг 4. Потоковая передача журналов из Службы приложений Azure для контейнера в Visual Studio Code
description: Шаг руководства 4. Просмотр журналов из Службы приложений Azure для отслеживания их поведения
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: b6c8d85ca74359863aca5b4e83cf96107e5a8e88
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709791"
---
# <a name="4-stream-logs-from-azure-app-service-for-a-container"></a>4: Потоковая передача журналов из Службы приложений Azure для контейнера

[Предыдущий шаг: внесение изменений и повторное развертывание](tutorial-deploy-containers-03.md)

Используйте эту процедуру для потоковой передачи журналов из Службы приложений Azure для контейнера в Visual Studio Code.

С помощью VS Code Вы можете просматривать (отслеживать) журналы из работающего сайта в Службе приложений Azure с регистрацией всех выходных данных в консоли (аналогично использованию инструкций `print`) и их перенаправлением на **панель вывода** VS Code.

1. Найдите приложение в обозревателе **Azure: Служба приложений**, щелкните приложение правой кнопкой мыши и выберите **Начать потоковую передачу журналов**.

1. Ответьте **Да** на предложение включить ведение журнала и перезапустить приложение. После перезапуска приложения откроется панель выходных данных VS Code с подключением к потоку журналов.

1. Через несколько секунд в окне выходных данных появится сообщение о том, что вы подключились к службе потоковой передачи журналов.

    <pre>
    Connecting to log stream...
    2018-09-27T20:14:26  Welcome, you are now connected to log-streaming service.

    2018-09-27 20:14:59.269 INFO  - Starting container for site

    2018-09-27 20:14:59.270 INFO  - docker run -d -p 24138:8000 --name vsdocs-django-sample-container_0 -e WEBSITES_PORT=8000 -e WEBSITE_SITE_NAME=vsdocs-django-sample-container -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_INSTANCE_ID=02c705ae24eaf5f298e553a9c2724b9fe4485707c2d1c36137cd02931091e561 -e HTTP_LOGGING_ENABLED=1 vsdocsregistry.azurecr.io/python-sample-vscode-django-tutorial:latest

    2018-09-27 20:15:06.216 INFO  - Container vsdocs-django-sample-container_0 for site vsdocs-django-sample-container initialized successfully.
    </pre>

1. Перейдите в приложение, чтобы просмотреть дополнительные выходные данные для разных HTTP-запросов.

> [!div class="nextstepaction"]
> [Журналы отображаются — перейти к шагу 5 >>>](tutorial-deploy-containers-05.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=04-stream-logs)
