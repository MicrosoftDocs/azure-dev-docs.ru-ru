---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 6cb841a9cf9563c3b7b3db86d907eac757c5d31d
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072669"
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

1. Протестируйте приложение. Для этого откройте браузер и перейдите по ссылке [http://localhost:3000](http://localhost:3000). Вот как должен выглядеть сайт:

    ![Выполнение приложения Express](../media/deploy-azure/express.png)

1. Используйте клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.
 
