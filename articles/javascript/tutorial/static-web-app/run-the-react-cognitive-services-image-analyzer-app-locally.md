---
title: Скачивание примера приложения React
description: Полный пример этого приложения размещен в репозитории GitHub. Создайте вилку этого репозитория, установите зависимости и запустите локальную версию.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5ddfd00be1c6160ec8c0c9630a8e950f13677bdd
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993563"
---
# <a name="2-download-and-run-the-react-cognitive-services-image-analyzer-app"></a>2. Скачивание и запуск приложения React для анализа изображений в Cognitive Services

Полный пример этого приложения размещен в репозитории GitHub. Создайте вилку этого репозитория, установите зависимости и запустите локальную версию.

## <a name="fork-the-sample-repo"></a>Создание вилки репозитория с примером

Создайте вилку репозитория, а не просто клонируйте его на локальный компьютер. Так вы получите собственный репозиторий GitHub, в который можно отправлять изменения. 

1. Откройте новое окно или вкладку браузера и войдите в <a href="https://github.com/login" target="_blank">GitHub</a>. 
1. Перейдите в веб-браузере к <a href="https://github.com/Azure-Samples/js-e2e-client-cognitive-services" target="_blank">репозиторию примера в GitHub</a>. 

    ```http
    https://github.com/Azure-Samples/js-e2e-client-cognitive-services
    ```

1. В верхней части страницы справа выберите команду **Fork** (Создать вилку). 
1. Выберите элемент **Code** (Код) и скопируйте URL-адрес расположения созданной вилки. 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-clone-github-sample-repository-fork.png" alt-text="Снимок части экрана: выбор варианта **Code** (Код) и копирование расположения вилки на веб-сайте GitHub.":::    

## <a name="create-local-development-environment"></a>Создание локальной среды разработки

1. В окне терминала или bash скопируйте вилку на локальный компьютер. Замените `YOUR-ACCOUNT-NAME` именем учетной записи GitHub.

    ```bash
    git clone https://github.com/YOUR-ACCOUNT-NAME/js-e2e-client-cognitive-services
    ```

1. Перейдите в новый каталог и установите зависимости. 

    ```bash
    cd js-e2e-client-cognitive-services && npm install
    ```

## <a name="run-sample"></a>Запуск примера

1. Запустите образец. 

    ```bash
    npm start
    ```

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-cognitive-services-app-before-authentication.png" alt-text="Снимок части экрана: браузер с примером приложения React для анализа изображений с использованием Компьютерного зрения Cognitive Services до настройки ключа и конечной точки.":::    
    
1. Остановите приложение. Закройте окно терминала или выполните в терминале команду `control+c`. 
    
## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Создание ресурса Компьютерного зрения и его применение в коде](create-computer-vision-resource-use-in-code.md) 