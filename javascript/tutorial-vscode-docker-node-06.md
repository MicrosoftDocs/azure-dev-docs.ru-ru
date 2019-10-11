---
title: Потоковая передача журналов из контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство, часть 5. Потоковая передача журналов приложения в Visual Studio Code
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 9df23cb6aac013006cf0f21871f16eededdcb816
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685989"
---
# <a name="stream-logs-into-visual-studio-code"></a>Потоковая передача журналов в Visual Studio Code

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-docker-node-05.md)

На этом этапе вы узнаете, как просмотреть любые выходные данные или вывести последний фрагмент данных, которые выполняющийся веб-сайт создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](media/deploy-containers/stream-logs-command.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](media/deploy-azure/enable-restart.png)

1. После перезапуска приложения в Visual Studio Code откроется панель **Вывод** с подключением к потоку журнала, на которой отображается сообщение `Starting Live Log Stream`.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-docker-node-07.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
