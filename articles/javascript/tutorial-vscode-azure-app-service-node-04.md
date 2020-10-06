---
title: Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code
description: Руководство по Node.js, часть 4. Просмотр журнала или вывод его последнего фрагмента
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: eed2663f32571dc40fb402553424c1303d43f7b9
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365237"
---
# <a name="stream-logs-from-azure-app-service"></a>Потоковая передача журналов из Службы приложений Azure

[Предыдущий шаг. Развертывание веб-сайта](tutorial-vscode-azure-app-service-node-03.md)

На этом этапе вы узнаете, как просмотреть любые выходные данные или вывести последний фрагмент данных, которые выполняющееся веб-приложение создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](media/deploy-azure/start-streaming-logs.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](media/deploy-azure/enable-restart.png)

1. После перезапуска приложения откроется окно **Вывод** в VS Code с подключением к потоку журнала для вывода данных.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Несколько раз обновите веб-страницу в браузере и убедитесь, что отображаются дополнительные выходные данные журнала.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-azure-app-service-node-05.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
