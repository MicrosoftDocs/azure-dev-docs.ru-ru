---
title: Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 4. Просмотр журнала или вывод его последнего фрагмента
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4048fd1d5d288d88cadf0a865c2c5b0ddd517daf
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709811"
---
# <a name="stream-logs-from-azure-app-service"></a>Потоковая передача журналов из Службы приложений Azure

[Предыдущий шаг. Развертывание веб-сайта](tutorial-vscode-azure-app-service-node-03.md)

На этом этапе вы узнаете, как просмотреть любые выходные данные или вывести последний фрагмент данных, которые выполняющийся веб-сайт создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](media/deploy-azure/view-logs.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](media/deploy-azure/enable-restart.png)

1. После перезапуска приложения откроется окно **Вывод** в VS Code с подключением к потоку журнала для вывода данных.

    <pre>
    Connecting to log-streaming service...
    2019-09-20 17:33:51.428 INFO  - Container msdocs-vscode-node_2 for site msdocs-vscode-node initialized successfully.
    2019-09-20 17:33:56.500 INFO  - Container logs
    </pre>

1. Несколько раз обновите веб-страницу в браузере и убедитесь, что отображаются дополнительные выходные данные журнала.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-azure-app-service-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
