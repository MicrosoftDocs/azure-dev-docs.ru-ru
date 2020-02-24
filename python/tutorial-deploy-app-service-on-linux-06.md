---
title: Шаг 6. Потоковая передача журналов из Службы приложений Azure в VS Code
description: 'Шаг 6 руководства: потоковая передача журналов приложения в Visual Studio Code'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: f2e19a52b673ddcef927165191d610776e06a32f
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422421"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6\. Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code

[Предыдущий шаг: развертывание приложения](tutorial-deploy-app-service-on-linux-05.md)

Используйте эту процедуру для потоковой передачи журналов из Службы приложений Azure в Visual Studio Code.

1. Откройте в Visual Studio Code обозреватель **Azure: Служба приложений**, щелкните Службу приложений правой кнопкой мыши и выберите действие **Start streaming logs** (Начать потоковую передачу журналов).

   ![Запуск потоковой передачи журналов из обозревателя Службы приложений](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

1. Выберите ответ **Да** в запросе на подтверждение включения журнала и перезапуска приложения. В процессе презапуска приложения в **окне вывода** VS Code отображается ход выполнения. Включение ведения журнала выполняется один раз.

1. Когда включение завершится, щелкните Службу приложений правой кнопкой мыши и выберите действие **Start streaming logs** (Начать потоковую передачу журналов). В **окне вывода** VS Code появится сообщение "Starting Live Log Stream" (Запуск потоковой передачи журнала) и начинается вывод данных журнала. Попробуйте обновить в браузере веб-приложение, чтобы получить дополнительные данные для журнала.

1. Чтобы отключить потоковую передачу журналов (не отключая ведение журнала), щелкните правой кнопкой мыши приложение в обозревателе **Azure: Служба приложений** и щелкните **Stop streaming logs** (Остановить потоковую передачу журналов).

> [!div class="nextstepaction"]
> [Журналы отображаются — перейти к шагу 7 >>>](tutorial-deploy-app-service-on-linux-07.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
