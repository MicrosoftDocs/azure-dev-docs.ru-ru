---
title: Развертывание веб-приложения Python в Службе приложений Azure в Linux с помощью VS Code
description: 'Шаг руководства 5: развертывание кода веб-приложения'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: e9b85928bd2b1308ab57747d7b0c32c085274cde
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019682"
---
# <a name="deploy-your-app"></a>Развертывание приложения

[Предыдущий шаг: настройка пользовательского файла запуска](tutorial-deploy-app-service-on-linux-04.md)

1. Откройте в Visual Studio Code обозреватель **Azure: Служба приложений** и щелкните синюю стрелку вверх.

   ![Команда развертывания в виде веб-приложения](media/deploy-azure/deploy-to-web-app-command.png)

    Можно также щелкнуть правой кнопкой мыши имя службы приложений и выбрать команду **Deploy to Web App** (Развернуть в веб-приложение).

1. В последующих приглашениях введите следующие сведения:

    - В поле "Выберите папку для развертывания" укажите текущую папку приложения.
    - В поле "Выберите веб-приложение" укажите приложение, которое вы создали на предыдущем шаге.

1. Вы можете следить за ходом развертывания в окне **Вывод** VS Code.

    ![Ход развертывания в окне вывода VS Code](media/deploy-azure/deployment-progress.png)

1. Через несколько минут, когда развертывание завершится (длительность зависит от количества устанавливаемых зависимостей), появится следующее сообщение. Нажмите кнопку **Обзор веб-сайта**, чтобы просмотреть работающий сайт.

    ![Сообщение о завершении развертывания](media/deploy-azure/deployment-complete.png)

    ![Приложение успешно запущено в Службе приложений](media/deploy-azure/running-app.png)

1. Чтобы проверить развертывание файлов, разверните службу приложений в обозревателе **Azure: Служба приложений**, а затем откройте узел **Файлы**.

    ![Проверка файлов развертывания с помощью обозревателя Службы приложений](media/deploy-azure/expand-files-node.png)

    Служба приложений создает виртуальную среду с указанными зависимостями в папке *antenv*. Развернув этот узел, вы сможете убедиться, что указанные в файле *requirements.txt* пакеты успешно установлены в папку *antenv/lib/python3.7/site-packages*.

> [!div class="nextstepaction"]
> [Я уже развернул приложение](tutorial-deploy-app-service-on-linux-06.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)
