---
title: Потоковая передача журналов из Службы приложений Azure
description: Руководство, часть 5. Просмотр журналов
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f96deb992af0d446876265e1b8214879ddff45e6
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "77709881"
---
# <a name="stream-logs-from-app-service"></a>Потоковая передача журналов из Службы приложений

[Предыдущий шаг. Развертывание приложения](tutorial-vscode-azure-cli-node-04.md)

На этом этапе вы просмотрите журналы (или выполните вывод последнего фрагмента) из запущенной Службы приложений. Все вызовы `console.log` в коде сайта отображаются в окне терминала.

1. Выполните следующую команду, чтобы начать ведение журнала, заменив `<your_app_name>` именем Службы приложений:

    ```azurecli
    az webapp log tail --name <your_app_name>
    ```

1. Через несколько секунд в окне выходных данных должно появиться сообщение о том, что вы подключились к службе потоковой передачи журналов.

    <pre>
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Несколько раз обновите страницу в браузере, чтобы создать дополнительные выходные данные.

    <pre>
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    </pre>

1. Нажмите клавиши **CTRL**+**C**, чтобы завершить сеанс ведения журнала.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-azure-cli-node-06.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
