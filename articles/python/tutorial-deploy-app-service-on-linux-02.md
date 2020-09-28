---
title: Шаг 2. Подготовка приложения к развертыванию в Службе приложений Azure в Linux с помощью Visual Studio Code
description: 'Руководство, шаг 2: настройка приложения'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 36e2c57f556718a0c2d83de90995ce52602a34ad
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832030"
---
# <a name="2-prepare-your-app-for-deployment-to-azure-app-service"></a>2: Подготовка приложения к развертыванию в Службе приложений Azure

[Предыдущий шаг: настройка среды](tutorial-deploy-app-service-on-linux-01.md)

В этой статье описано, как подготовить приложение для развертывания в Службе приложений Azure. Вы можете использовать существующее приложение, а также создать или скачать новое.

Если у вас уже есть приложение, с которым вы хотите работать, проверьте наличие файла *requirements.txt*, который описывает все зависимости, включая платформы Flask или Django. Вы можете использовать любую выбранную платформу.

Если у вас еще нет такого приложения, выберите один из параметров ниже. Убедитесь в том, что приложение успешно выполняется локально.

## <a name="option-1-vs-code-flask-tutorial-sample"></a>Вариант 1. Пример из учебника по работе с Flask в VS Code

Скачайте или клонируйте пример [https://github.com/Microsoft/python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), созданный при работе с [руководством по Flask](https://code.visualstudio.com/docs/python/tutorial-flask).

## <a name="option-2-vs-code-django-tutorial-sample"></a>Вариант 2. Пример из учебника по работе с Django в VS Code

Скачайте или клонируйте пример [https://github.com/Microsoft/python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial), созданный при работе с [руководством по Django](https://code.visualstudio.com/docs/python/tutorial-django).

Если приложение Django использует локальную базу данных SQLite, как в этом примере, следует добавить в репозиторий предварительно инициализированную и заполненную копию файла *db.sqlite3*. Это связано с тем, что в настоящее время Служба приложений для Linux не позволяет выполнить при развертывании команду Django `migrate`, а значит вам придется развернуть предварительно подготовленную базу данных. Даже в этом случае база данных по сути доступна только для чтения, ведь попытка записи в нее приводит к ошибкам.

В любом случае лучше всего использовать отдельную базу данных, которая разворачивается и инициализируется независимо от кода приложения.

## <a name="option-3-create-a-minimal-flask-app"></a>Способ 3. Создание минимального приложения Flask

В этом разделе описывается минимальное приложение Flask, которое используется в этом пошаговом руководстве.

1. Создайте новую папку, откройте ее в VS Code и добавьте файл с именем *hello.py* и следующим содержимым. Объекту приложения специально присвоено имя `myapp`, чтобы продемонстрировать использование имен в команде запуска для Службы приложений, которое описано далее.

    ```python
    from flask import Flask
    myapp = Flask(__name__)

    @myapp.route("/")
    def hello():
        return "Hello Flask, on Azure App Service for Linux"
    ```

1. Создайте файл *requirements.txt* со следующим содержимым.

    ```text
    Flask==1.1.2
    ```

1. Откройте терминал, выполнив команду меню **Терминал** > **Новый терминал**.

1. В терминале создайте и активируйте виртуальную среду с именем `env`.

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux)

    ```bash
    sudo apt-get install python3-venv    # If needed
    python3 -m venv env
    source env/bin/activate
    ```

    # <a name="windows"></a>[Windows](#tab/windows)

    ```cmd
    python -m venv env
    env\scripts\activate
    ```

    ---

1. Установите зависимости приложения:

    ```cmd
    pip install -r requirements.txt
    ```

1. Переменная среды FLASK_APP сообщает Flask, где найти объект приложения.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set FLASK_APP=hello:myapp
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```ps
    $env:FLASK_APP = "hello:myapp"
    ```

   # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export FLASK_APP=hello:myapp
    ```

    ---

1. Запустите приложение:

    ```cmd
    flask run
    ```

1. Затем откройте приложение в браузере по URL-адресу `http://127.0.0.1:5000/`.

> [!div class="nextstepaction"]
> [Мое приложение готово — перейти к шагу 3 >>>](tutorial-deploy-app-service-on-linux-03.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)
