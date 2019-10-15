---
title: Руководство по Подготовка приложения к развертыванию в Службе приложений Azure в Linux с помощью Visual Studio Code
description: 'Руководство, шаг 2: настройка приложения'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: b284dd6b5a5d1a09f1be48fb2ab7e6a8f95a4708
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172361"
---
# <a name="tutorial-prepare-your-app-for-deployment-to-azure-app-service"></a>Руководство по Подготовка приложения к развертыванию в Службе приложений Azure

[Предыдущий шаг: предварительные требования](tutorial-deploy-app-service-on-linux-01.md)

Если у вас уже есть приложение, с которым вы хотите работать, проверьте наличие файла *requirements.txt*, который описывает все зависимости, включая платформы Flask или Django.

Если у вас еще нет такого приложения, выберите один из параметров ниже. Убедитесь в том, что приложение успешно выполняется локально.

## <a name="minimal-flask-app"></a>Минимальное приложение Flask

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
    Flask==1.1.1
    ```

1. Следуйте инструкциям в [учебнике по созданию среды проекта для Flask](https://code.visualstudio.com/docs/python/tutorial-flask#create-a-project-environment-for-flask), чтобы создать виртуальную среду с установленной платформой Flask, которая позволит локально запускать приложения.

1. Запустите приложение с помощью одной из следующих команд (в зависимости от операционной системы). Переменная среды FLASK_APP сообщает Flask, где найти объект приложения.

    ```ps
    set FLASK_APP=hello:myapp
    flask run
    ```

    ```bash
    export FLASK_APP=hello:myapp
    flask run
    ```

    Затем откройте приложение в браузере по URL-адресу `http://127.0.0.1:5000/`.

## <a name="vs-code-flask-tutorial-sample"></a>Пример из учебника по работе с Flask в VS Code

Скачайте или скопируйте пример [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), который является результатом выполнения [руководства по Flask](https://code.visualstudio.com/docs/python/tutorial-flask).

## <a name="vs-code-django-tutorial-sample"></a>Пример из учебника по работе с Django в VS Code

Скачайте или скопируйте пример [python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial), который является результатом выполнения [руководства по Django](https://code.visualstudio.com/docs/python/tutorial-django).

Если приложение Django использует локальную базу данных SQLite, как в этом примере, следует добавить в репозиторий предварительно инициализированную и заполненную копию файла *db.sqlite3*. Это связано с тем, что в настоящее время Служба приложений для Linux не позволяет выполнить при развертывании команду Django `migrate`, а значит вам придется развернуть предварительно подготовленную базу данных. Даже в этом случае база данных по сути доступна только для чтения, ведь попытка записи в нее приводит к ошибкам.

В любом случае лучше всего использовать отдельную базу данных, которая разворачивается и инициализируется независимо от кода приложения.

> [!div class="nextstepaction"]
> [Мое приложение готово](tutorial-deploy-app-service-on-linux-03.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=02-prepare-app)
