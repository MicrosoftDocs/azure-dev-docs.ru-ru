---
title: Создание экземпляра Службы приложений Azure с помощью Visual Studio Code
description: Руководство, часть 2. Создание приложения Deno и его запуск в локальной среде
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 32bd9e1f2b30d57ad573eb9be3fa22b9e246b0e9
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792537"
---
# <a name="test-local-deno-apps"></a>Тестирование локальных приложений Deno

[Предыдущий шаг. Общие сведения и предварительные требования](tutorial-visual-studio-code-azure-app-service-deno-01.md)

На этом шаге вы создадите простой API Deno с помощью встроенного веб-сервера Deno. Затем вы запустите приложение локально.

## <a name="create-and-run-a-local-deno-app"></a>Создание и запуск локального приложения Deno

1. В окне терминала или командной строки перейдите в расположение, в котором нужно создать папку приложения, и создайте новую папку с именем `deno-demo`.

1. Создайте файл с именем `demo.ts`.
1. Deno принимает выполняющийся код непосредственно из URL-адресов. Создайте HTTP-сервер, который на все запросы отвечает строкой "Hello World". Используйте следующий код:

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. Запустите приложение, выполнив следующий скрипт:

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. Протестируйте приложение, перейдя по ссылке `http://localhost:80` в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение демо-сервера](media/deploy-azure/deno-hello-world.png)

    Этот код также можно выполнить с помощью команды `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`.

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.

> [!div class="nextstepaction"]
> [Приложение Deno создано](tutorial-visual-studio-code-azure-app-service-deno-03.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=create-app)

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
