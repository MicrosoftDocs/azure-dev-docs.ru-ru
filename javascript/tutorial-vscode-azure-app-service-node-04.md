---
title: Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 4. Просмотр журнала или вывод его последнего фрагмента
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 8484efc27120d374738a933244a3fce71c7c6acb
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686211"
---
# <a name="stream-logs-from-azure-app-service"></a>Потоковая передача журналов из Службы приложений Azure

[Предыдущий шаг. Развертывание веб-сайта](tutorial-vscode-azure-app-service-node-03.md)

На этом этапе вы узнаете, как просмотреть любые выходные данные или вывести последний фрагмент данных, которые выполняющийся веб-сайт создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](media/deploy-azure/view-logs.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](media/deploy-azure/enable-restart.png)

1. После перезапуска приложения откроется окно **Вывод** в VS Code с подключением к потоку журнала для вывода данных.

    ```bash
    Connecting to log-streaming service...
    2019-09-20 17:33:51.428 INFO  - Container msdocs-vscode-node_2 for site msdocs-vscode-node initialized successfully.
    2019-09-20 17:33:56.500 INFO  - Container logs
    ```

1. Несколько раз обновите веб-страницу в браузере и убедитесь, что отображаются дополнительные выходные данные журнала.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-azure-app-service-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
