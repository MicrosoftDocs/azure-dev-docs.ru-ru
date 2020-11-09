---
title: Включить файл 2
description: Включить файл 2
ms.topic: include
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 05f3e5fb847e7589e41d041736c986dce0bc5b29
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278698"
---
На этом шаге вы создадите простой API Deno с помощью встроенного веб-сервера Deno. Затем вы запустите приложение локально.

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

    ![Выполнение демо-сервера](../../media/deploy-azure/deno-hello-world.png)

    Этот код также можно выполнить с помощью команды `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`.

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.
