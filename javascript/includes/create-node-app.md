---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 59eea52aab3f2b1941a3accb5848bc4ad92d2992
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740514"
---
1. В окне терминала или командной строки перейдите в расположение, в котором нужно создать папку приложения.

1. Выполните приведенную ниже команду, чтобы с помощью генератора Express создать приложение Express с именем *myexpressapp*. В параметрах `--git --view pug` указано, что генератор должен создать файл с расширением *.gitignore* и использовать обработчик шаблонов [pug](https://pugjs.org/api/getting-started.html), ранее известный как Jade.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Перейдите в папку приложения:

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

1. Протестируйте приложение, перейдя по ссылке [http://localhost:3000](http://localhost:3000) в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение приложения Express](../media/deploy-azure/express.png)

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.
