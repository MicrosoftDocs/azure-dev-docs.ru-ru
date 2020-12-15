---
title: Развертывание приложения Express.js/MongoDB с помощью VSCode в Службе приложений/CosmosDB
description: В этом руководстве вы будете работать с приложением Node.js с базой данных MongoDB, используя нативный API MongoDB. Разверните приложение Node.js в Службе приложений Azure (в Linux), а затем проверьте работу этого размещенного приложения.
ms.topic: tutorial
ms.date: 12/03/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 8c40801607e11a4b929f0bb76926122d26217805
ms.sourcegitcommit: 550b165d0b910f4ea9652d8401dd4fc93f057f05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96610976"
---
# <a name="deploy-expressjs-mongodb-app-to-app-service-from-visual-studio-code"></a>Развертывание приложения MongoDB на платформе Express.js в Службе приложений с помощью Visual Studio Code

Разверните приложение Express.js, которое подключается к MongoDB, в Службе приложений Azure (в Linux) и CosmosDB. 

Вся работа по программированию уже сделана заранее, и это руководство полностью посвящено эффективному использованию локальных и удаленных сред Azure в Visual Studio Code с расширениями Azure.

## <a name="top-tasks"></a>Основные задачи

В этом руководстве представлено несколько **основных задач Azure** для разработчиков JavaScript:

* создание ресурса CosmosDB для размещения базы данных MongoDB;
* создание ресурса Службы приложений для размещения приложения Express.js;
* развертывание приложения Express.js в Службе приложений.

## <a name="sample-application"></a>Пример приложения

[Пример приложения Express.js](https://github.com/Azure-Samples/js-e2e-express-mongo) состоит из следующих элементов:

* сервер **Express.js**, размещенный на порте 8080;
* простая подсистема **представления React.js на стороне сервера**;
* функции **собственного API MongoDB** для вставки, удаления и поиска данных.

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Простое приложение Node.js, подключенное к базе данных MongoDB.":::

## <a name="create-or-use-existing-azure-subscription"></a>Создание новой или использование существующей подписки Azure 

* Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/).

## <a name="install-software"></a>Установка ПО

- Установите на локальный компьютер [Node.js 12 (LTS) и npm](https://nodejs.org/en/download) (диспетчер пакетов Node.js).
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
- Расширения Visual Studio Code:
    - [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для Visual Studio Code (устанавливается из Visual Studio Code).
    - [Базы данных Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="create-a-cosmosdb-database-resource-for-mongodb"></a>Создание ресурса базы данных CosmosDB для MongoDB

Сначала создайте ресурс Cosmos, так как это займет несколько минут. 

1. В Visual Studio Code щелкните значок **Azure** в крайнем левом меню, а затем выберите раздел **Базы данных**. 

    Если раздел **Базы данных** не отображается, убедитесь, что в верхнем меню Azure **...** выбран этот раздел. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-azure-extension-select-database-section.png" alt-text="Снимок части экрана, на котором показан значок удаленного контейнера в Visual Studio Code"::: 

1. В разделе **Базы данных** в обозревателе Azure щелкните подписку правой кнопкой мыши, а затем выберите пункт **Создать сервер**.
1. В палитре команд в разделе **Create new Azure Database Server** (Создать новый сервер Базы данных Azure) выберите **Azure Cosmos DB for MongoDB API** (Azure Cosmos DB для API MongoDB). 
1. Выполните инструкции из следующей таблицы, чтобы разобраться в применении значений разных свойств. На создание базы данных может потребоваться до 15 минут.

    |Свойство|Значение|
    |--|--|
    |Введите глобально уникальное **имя учетной записи** для нового ресурса.| Введите значение для ресурса, например `cosmos-mongodb-YOUR-NAME`. Замените строку `YOUR-NAME` именем или уникальным идентификатором. Это уникальное имя также добавляется в URL-адрес для доступа к этому ресурсу в браузере.|
    |Выберите или создайте группу ресурсов.|Если вам нужно создать группу ресурсов, примените соглашение об именовании с указанием владельца, назначения и региона, например `westus-cosmostutorial-joesmith`.|
    |Расположение|Расположение ресурса. Для работы с этим руководством выберите ближайший к вам регион.|

    Создание ресурса может занять до 15 минут. Если вы ограничены по времени, пропустите следующий раздел, но не забудьте вернуться к нему через несколько минут, чтобы выполнить описанные здесь действия.

## <a name="get-cosmosdb-connection-string"></a>Получение строки подключения CosmosDB

В том же окне обозревателя баз данных Azure щелкните имя ресурса правой кнопкой мыши, и выберите **Копирование строки подключения**, чтобы скопировать строку подключения. Она понадобится позже в рамках работы с учебником по файлу переменной среды.

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-databases-extenion-copy-connection-string.png" alt-text="Форма веб-приложения Express.js и результат обработки данных из локального развертывания MongoDB.":::

## <a name="download-and-run-the-sample-expressjs-app"></a>Скачивание и запуск примера приложения Express.js.

Вам предоставляется веб-приложение Express.js. Скачайте приложение, установите зависимости и запустите приложение. 

1. [Скачайте ZIP-файл репозитория GitHub](https://github.com/Azure-Samples/js-e2e-express-mongo.git) на локальный компьютер, а затем разверните его в локальную папку. 
1. Откройте эту папку в Visual Studio Code. Для этого можно щелкнуть папку правой кнопкой мыши и выбрать действие **Open with Code** (Открыть в Code) или выполнить в этой папке эквивалентную команду интерфейса командной строки:

    ```bash
    code .
    ```

1. Измените файл среды (`.env`), добавив свойство строки подключения для CosmosDB в качестве значения свойства `DATABASE_URL`. 

    ```bash
    ENVIRONMENT=development
    DATABASE_NAME=
    DATABASE_COLLECTION_NAME=
    DATABASE_URL=
    ```

1. В Visual Studio Code откройте окно терминала и выполните указанные ниже команды, чтобы установить зависимости примера и запустить веб-приложение.

    ```bash
    npm install && npm start
    ```

1. Проверьте веб-приложение на локальном компьютере в браузере.

    ```url
    http://localhost:8080/
    ```

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Простое приложение Node.js, подключенное к базе данных MongoDB.":::

## <a name="create-web-app-resource-and-deploy-expressjs-app"></a>Создание ресурса веб-приложения и развертывание приложения Express.js

Примените расширение Visual Studio Code для Службы приложений, чтобы создать ресурс Службы приложений и развернуть в ресурсе веб-приложение Express.js.

1. Перейдите в обозреватель Azure. Щелкните подписку правой кнопкой мыши и выберите действие `Create new web app...`.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Снимок части экрана Visual Studio Code, на котором показано, как расширение Службы приложений Azure применяется для создания веб-приложения.":::

1. Выполните инструкции из следующей таблицы, чтобы разобраться в применении значений разных свойств.

    |Свойство|Значение|
    |--|--|
    |Введите глобально уникальное имя для нового веб-приложения.| Введите значение для ресурса Службы приложений, например `web-app-with-mongodb-YOUR-NAME`. Замените строку `<YOUR-NAME>` именем или уникальным идентификатором. Это уникальное имя также добавляется в URL-адрес для доступа к этому ресурсу в браузере.|
    |Выберите среду выполнения для приложения Linux.|Выберите `Node 12 LTS`.|

1. Когда процесс создания приложения завершится, в нижнем правом углу Visual Studio Code отобразится сообщение о состоянии с возможностью выбрать действие `Deploy` или `View output`. Выберите `Deploy`.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Снимок части экрана Visual Studio Code: использование расширения Службы приложений Azure для развертывания веб-приложения после его создания.":::

    Если сообщение о состоянии исчезнет, для запуска развертывания откройте обозреватель Azure и щелкните правой кнопкой мыши имя ресурса, а затем выберите пункт **Развертывание в виде веб-приложения...** .

1. В процессе развертывания вы увидите уведомление, которое позволяет открыть **окно вывода**.  В нем отображается пошаговое состояние развертывания. 

1. Когда развертывание успешно завершится, вы увидите соответствующее уведомление. Щелкните **Журналы потоковой передачи**, чтобы просмотреть текущие журналы. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="Служба успешно развернута. Выбор элемента &quot;Журналы потоковой передачи&quot;.":::

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="Когда развертывание завершится, появится уведомление с возможностью выбрать &quot;Журналы потоковой передачи&quot;.":::    

1. Откройте веб-сайт в браузере и замените текст `YOUR-RESOURCE_NAME` именем своего ресурса: `https://YOUR-RESOURCE_NAME.azurewebsites.net`.
1. Используйте веб-приложение, добавляя и удаляя элементы. 

## <a name="clean-up-resources"></a>Очистка ресурсов 

После завершения работы с этим руководством необходимо удалить два созданных ресурса, чтобы за них не начислялась плата. 

1. В Visual Studio Code откройте обозреватель баз данных Azure, щелкните ресурс правой кнопкой мыши и выберите **Удаление учетной записи хранения…**
1. В Visual Studio Code откройте обозреватель Службы приложений Azure, щелкните ресурс правой кнопкой мыши и выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Продолжите изучение Службы приложений и CosmosDB:
* [Настройка приложения Node.js для Службы приложений Azure](/azure/app-service/configure-language-nodejs?pivots=platform-linux)
* [Подключение по SSH](/azure/app-service/configure-linux-open-ssh-session)
* [Перенос данных в CosmosDB](/azure/dms/tutorial-mongodb-cosmos-db?toc=/azure/cosmos-db/toc.json?toc=/azure/cosmos-db/toc.json)
