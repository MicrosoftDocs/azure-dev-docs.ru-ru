---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: 9db8df165bd4d0f42694ff1e752878c8f055bce8
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218403"
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
 
