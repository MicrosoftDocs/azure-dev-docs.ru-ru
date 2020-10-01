---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 66e71a8632b622a090c9899e0354d3e70c768c21
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364716"
---
1. В окне командной строке терминала перейдите к расположению, в котором нужно создать папку приложения.

1. Выполните приведенную ниже команду, чтобы с помощью генератора Express создать приложение Express с именем `myexpressapp`. В параметрах `--git --view pug` указано, что генератор должен создать файл GITIGNORE и использовать обработчик шаблонов [Pug](https://pugjs.org/api/getting-started.html) (ранее известный под именем Jade).

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Перейдите к папке приложения:

    ```bash
    cd myexpressapp
    ```

1. Установите зависимости приложения:

    ```bash
    npm install
    ```

1. Запустите сервер:

    ```bash
    npm start
    ```

1. Протестируйте приложение. Для этого откройте браузер и перейдите по ссылке `http://localhost:3000`. Вот как должен выглядеть сайт:

    ![Выполнение приложения Express](../media/deploy-azure/express.png)

1. Используйте клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.
 
