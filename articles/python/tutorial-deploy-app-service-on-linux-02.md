---
title: Шаг 2. Подготовка приложения к развертыванию в Службе приложений Azure в Linux с помощью Visual Studio Code
description: 'Руководство, шаг 2: настройка приложения'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 78efb3fdfa7d7d4ac8699726c2be6b17d27ea875
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911394"
---
# <a name="2-prepare-your-app-for-deployment-to-azure-app-service"></a>2: Подготовка приложения к развертыванию в Службе приложений Azure

[Предыдущий шаг: настройка среды](tutorial-deploy-app-service-on-linux-01.md)

В этой статье описано, как подготовить приложение для развертывания в Службе приложений Azure. Вы можете использовать существующее приложение, а также создать или скачать новое.

## <a name="if-you-already-have-an-app"></a>Если у вас уже есть приложение

Если у вас уже есть приложение, с которым вы хотите работать, проверьте, есть ли в корневой папке этого проекта файл *requirements.txt* с полным списком зависимостей, включая платформы Flask, Django и другие. Вы можете использовать любую выбранную платформу.

> [!div class="nextstepaction"]
> [Мое приложение готово — перейти к шагу 3 >>>](tutorial-deploy-app-service-on-linux-03.md)

## <a name="if-you-dont-already-have-an-app"></a>Если приложения еще нет

Если у вас еще нет приложения, выберите *один* из предложенных ниже вариантов. Убедитесь в том, что приложение успешно выполняется локально.

В оставшейся части этого руководства используется код, представленный в [варианте 3](#option-3-create-a-minimal-flask-app).

### <a name="option-1-use-the-vs-code-flask-tutorial-sample"></a>Вариант 1. Использование примера из руководства по работе с Flask в VS Code

Скачайте или клонируйте пример [https://github.com/Microsoft/python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), созданный при работе с [руководством по Flask](https://code.visualstudio.com/docs/python/tutorial-flask). Обратите внимание, что файл с кодом приложения находится в папке *hello_app*. Инструкции по локальному запуску приложения см. в файле *readme.md* примера.

### <a name="option-2-use-the-vs-code-django-tutorial-sample"></a>Вариант 2. Использование примера из руководства по работе с Django в VS Code

Скачайте или клонируйте пример [https://github.com/Microsoft/python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial), созданный при работе с [руководством по Django](https://code.visualstudio.com/docs/python/tutorial-django).

В идеале развернутые в облаке приложения Django должны использовать и облачную базу данных, например PostgreSQL для Azure. Дополнительные сведения см. в статье [Учебник. Развертывание веб-приложения Django с PostgreSQL с помощью портала Azure](tutorial-python-postgresql-app-portal.md).

Если приложение Django использует локальную базу данных SQLite, как в этом примере, то для целей этого руководства проще всего добавить в репозиторий предварительно инициализированную и заполненную копию файла *db.sqlite3*. Если нет, нужно будет настроить выполняемую после сборки команду для выполнения команды Django `migrate` в том же контейнере, где развернуто приложение. Дополнительные сведения см. в разделе ["Настройка автоматизации сборки" статьи о настройке Службы приложений](/azure/app-service/configure-language-python#customize-build-automation).

### <a name="option-3-create-a-minimal-flask-app"></a>Способ 3. Создание минимального приложения Flask

В этом разделе описывается минимальное приложение Flask, которое используется в этом пошаговом руководстве.

1. Создайте новую папку, откройте ее в VS Code и добавьте файл с именем *hello.py* и следующим содержимым. Объекту приложения специально присвоено имя `myapp`, чтобы продемонстрировать использование имен в команде запуска для Службы приложений, которое описано далее.

    ```python
    from flask import Flask
    myapp = Flask(__name__)

    @myapp.route("/")
    def hello():
        return "Hello Flask, on Azure App Service for Linux"
    ```

1. В той же папке создайте файл *requirements.txt* со следующим содержимым:

    ```text
    Flask
    ```

1. Откройте терминал, выполнив команду меню **Терминал** > **Новый терминал**.

1. В окне терминала перейдите в папку, содержащую файл *hello.py*. Все остальные команды терминала выполняются в этой папке.

1. Создайте и активируйте виртуальную среду с именем `.venv`:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    :: Assumes Windows
    py -3 -m venv .venv
    .venv\scripts\activate
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```ps
    # Assumes Windows
    py -3 -m venv .venv
    .venv\scripts\activate
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    # Assumes macOS/Linux
    sudo apt-get install python3-venv    # If needed
    python3 -m venv .venv
    source .venv/bin/activate
    ```

    ---

1. Когда VS Code предложит активировать только что созданную среду, выберите ответ **Да**.

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

1. Откройте приложение в браузере по URL-адресу `http://127.0.0.1:5000/`. Должно отобразиться сообщение "Hello Flask, on Azure App Service for Linux".

1. Остановите работу сервера с помощью сочетания клавиш **CTRL**+**C** в окне терминала.

> [!div class="nextstepaction"]
> [Мое приложение готово — перейти к шагу 3 >>>](tutorial-deploy-app-service-on-linux-03.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)
