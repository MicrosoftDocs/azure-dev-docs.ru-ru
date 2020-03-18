---
title: Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 4. Просмотр журнала или вывод его последнего фрагмента
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: e6c1f4f87a28b33dac51d6ffc59c0cd9dfdc429d
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893531"
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
