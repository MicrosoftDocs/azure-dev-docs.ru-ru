---
title: Потоковая передача журналов из контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство по Docker, часть 7. Потоковая передача журналов в Visual Studio Code
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: ba651244cc3fac3bf0f39c4f79607d1d3e997988
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364607"
---
# <a name="stream-logs-into-visual-studio-code"></a>Потоковая передача журналов в Visual Studio Code

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-docker-node-06.md)

На этом этапе вы узнаете, как просмотреть любые выходные данные или вывести последний фрагмент данных, которые выполняющийся веб-сайт создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](media/deploy-containers/stream-logs-command.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](media/deploy-azure/enable-restart.png)

1. После перезапуска приложения в Visual Studio Code откроется панель **Вывод** с подключением к потоку журнала, на которой отображается сообщение `Starting Live Log Stream`.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-docker-node-08.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
