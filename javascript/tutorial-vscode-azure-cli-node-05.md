---
title: Потоковая передача журналов из Службы приложений Azure
description: Руководство, часть 5. Просмотр журналов
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: ce112d535d1f89a7e153f80b9fb80032a977b8d8
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686145"
---
# <a name="stream-logs-from-app-service"></a>Потоковая передача журналов из Службы приложений

[Предыдущий шаг. Развертывание приложения](tutorial-vscode-azure-cli-node-04.md)

На этом этапе вы просмотрите журналы (или выполните вывод последнего фрагмента) из запущенной Службы приложений. Все вызовы `console.log` в коде сайта отображаются в окне терминала.

1. Выполните следующую команду, чтобы начать ведение журнала, заменив `<your_app_name>` именем Службы приложений:

    ```bash
    az webapp log tail --name <your_app_name>
    ```

1. Через несколько секунд должно появиться сообщение о том, что вы подключены к службе потоковой передачи журналов.

    ```bash
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    ```

1. Несколько раз обновите страницу в браузере, чтобы создать дополнительные выходные данные.

    ```bash
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    ```

1. Нажмите клавиши **CTRL**+**C**, чтобы завершить сеанс ведения журнала.

> [!div class="nextstepaction"]
> [Журналы отображаются](tutorial-vscode-azure-cli-node-06.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
