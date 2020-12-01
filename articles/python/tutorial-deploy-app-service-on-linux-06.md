---
title: Шаг 6. Потоковая передача журналов из Службы приложений Azure в VS Code
description: 'Шаг 6 руководства: потоковая передача журналов приложения в Visual Studio Code'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 01df1219302f4edf21845c999337354a065352ac
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485683"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6\. Потоковая передача журналов из Службы приложений Azure с помощью Visual Studio Code

[Предыдущий шаг: развертывание приложения](tutorial-deploy-app-service-on-linux-05.md)

Используйте эту процедуру для потоковой передачи журналов из Службы приложений Azure в Visual Studio Code.

1. Откройте в Visual Studio Code обозреватель **Azure: Служба приложений**, щелкните Службу приложений правой кнопкой мыши и выберите действие **Start streaming logs** (Начать потоковую передачу журналов).

   ![Запуск потоковой передачи журналов из обозревателя Службы приложений](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

   Если поступит запрос на подтверждение включения журнала и перезапуска приложения, выберите ответ **Да**. В процессе презапуска приложения в **окне вывода** VS Code отображается ход выполнения. После перезапуска приложения снова выберите команду **Start streaming logs** (Начать потоковую передачу журналов). Включение ведения журнала выполняется один раз.

1. В **окне вывода** VS Code появится сообщение "Starting Live Log Stream" (Запуск потоковой передачи журнала) и начинается вывод данных журнала. Попробуйте обновить в браузере веб-приложение, чтобы получить дополнительные данные для журнала.

1. Чтобы отключить потоковую передачу журналов (не отключая ведение журнала), щелкните правой кнопкой мыши приложение в обозревателе **Azure: Служба приложений** и щелкните **Stop streaming logs** (Остановить потоковую передачу журналов).

> [!div class="nextstepaction"]
> [Журналы отображаются — перейти к шагу 7 >>>](tutorial-deploy-app-service-on-linux-07.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)
