---
title: Руководство по Потоковая передача журналов из Службы приложений Azure в VS Code
description: 'Шаг 6 руководства: потоковая передача журналов приложения в Visual Studio Code'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: f4aac132e9c01a0c428e243e06e811357defc2aa
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172230"
---
# <a name="tutorial-stream-logs-from-azure-app-service-into-visual-studio-code"></a>Руководство по Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code

[Предыдущий шаг: развертывание приложения](tutorial-deploy-app-service-on-linux-05.md)

1. Откройте в Visual Studio Code обозреватель **Azure: Служба приложений**, щелкните Службу приложений правой кнопкой мыши и выберите действие **Start streaming logs** (Начать потоковую передачу журналов).

   ![Команда запуска потоковой передачи журналов](media/deploy-azure/start-streaming-logs-command.png)

1. Выберите ответ **Да** в запросе на подтверждение включения журнала и перезапуска приложения. В процессе презапуска приложения в **окне вывода** VS Code отображается ход выполнения. Включение ведения журнала выполняется один раз.

1. Когда включение завершится, щелкните Службу приложений правой кнопкой мыши и выберите действие **Start streaming logs** (Начать потоковую передачу журналов). В **окне вывода** VS Code появится сообщение "Starting Live Log Stream" (Запуск потоковой передачи журнала) и начинается вывод данных журнала. Попробуйте обновить в браузере веб-приложение, чтобы получить дополнительные данные для журнала.

1. Чтобы отключить потоковую передачу журналов (не отключая ведение журнала), щелкните правой кнопкой мыши приложение в обозревателе **Azure: Служба приложений** и щелкните **Stop streaming logs** (Остановить потоковую передачу журналов).

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-deploy-app-service-on-linux-07.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
