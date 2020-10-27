---
title: include file tutorial-azure-web-app-mongodb-02.md
description: include file tutorial-azure-web-app-mongodb-02.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: fafb2b452e9e1bfab34d9c2d9d46adaabf4fd022
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183918"
---
В этом разделе руководства показано, как скачать пример приложения на локальный компьютер и запустить его в терминале Visual Studio Code. Затем вы сможете открыть локально запущенное приложение в браузере. 

## <a name="download-and-run-the-initial-expressjs-app"></a>Скачивание и запуск начальной версии приложения Express.js

Начальная версия веб-приложения Express.js предоставляется в качестве отправной точки. В этой процедуре вы скачаете приложение, установите зависимости и запустите приложение. Приложение начальной версии попытается подключиться к базе данных, если она доступна. Но даже при отсутствии базы данных веб-сайт успешно отвечает на запросы. 

1. [Скачайте ZIP-файл репозитория GitHub](https://github.com/Azure-Samples/js-e2e-express-mongo.git) на локальный компьютер, а затем разверните его в локальную папку. 
1. Откройте эту папку в Visual Studio Code. Для этого можно щелкнуть папку правой кнопкой мыши и выбрать действие **Open with Code** (Открыть в Code) или выполнить в этой папке эквивалентную команду интерфейса командной строки:

    ```console
    code .
    ```

1. В Visual Studio Code откройте окно терминала и выполните следующую команду, чтобы установить зависимости этого примера.

    ```javascript
    npm install
    ```

1. В том же окне терминала выполните команду для запуска веб-приложения.

    ```javascript
    npm start
    ```

1. Чтобы просмотреть веб-приложение на локальном компьютере, откройте страницу с приведенным ниже URL-адресом в любом веб-браузере.

    ```url
    http://localhost:8080/
    ```

    Если в браузере отобразится простое веб-приложение с текстовым сообщением о том, что база данных не найдена, значит вы успешно выполнили инструкции из этого раздела руководства.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Простое приложение Node.js, подключенное к базе данных MongoDB.":::