---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 6f9e5e8f30c5108996f14bef9dc4c90a668c60c1
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405295"
---
1. Убедитесь, что вы находитесь в папке *python-docs-hello-world*. 

1. Создайте виртуальную среду и установите необходимые зависимости.

    [!include [virtual environment setup](../app-service-quickstart-python-venv.md)]

    Если вы видите ошибку "[Errno 2] Не существует такого файла или папки: requirements.txt", убедитесь, что вы находитесь в папке *python-docs-hello-world*.

1. Запустите сервер разработки.

    ```terminal  
    flask run
    ```
    
    По умолчанию сервер предполагает, что модулем записи приложения является *app.py* , как в примере. (Если вы используете другое имя модуля, задайте это имя для переменной среды `FLASK_APP`.)

1. Откройте веб-браузер и перейдите к примеру приложения по адресу `http://localhost:5000/`. Приложение отображает сообщение **Hello World!** .

    ![Локальный запуск примера приложения Python](../../media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. В окне терминала нажмите клавиши **CTRL**+**C** , чтобы выйти из сервера разработки.
