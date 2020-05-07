---
title: Шаг 5. Развертывание веб-приложения Python в Службе приложений Azure в Linux с помощью VS Code
description: 'Шаг руководства 5: развертывание кода веб-приложения'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 3f17fe3190857d1cc4faca84c9319e1c514cea4c
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "80441209"
---
# <a name="5-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>5: Развертывание веб-приложения Python в Службе приложений Azure в Linux

[Предыдущий шаг: настройка пользовательского файла запуска](tutorial-deploy-app-service-on-linux-04.md)

Используйте эту процедуру для развертывания приложения Python в Службе приложений Azure.

1. Откройте в Visual Studio Code обозреватель **Azure: Служба приложений** и щелкните синюю стрелку вверх.

   ![Развертывание веб-приложения в Службе приложений в обозревателе Службы приложений](media/deploy-azure/deploy-web-app-to-app-service-in-app-service-explorer.png)

    Можно также щелкнуть правой кнопкой мыши имя службы приложений и выбрать команду **Deploy to Web App** (Развернуть в веб-приложение).

1. В последующих приглашениях введите следующие сведения:

    - В поле "Выберите папку для развертывания" укажите текущую папку приложения.
    - В поле "Выберите веб-приложение" укажите приложение, которое вы создали на предыдущем шаге.

1. Вы можете следить за ходом развертывания в окне **Вывод** VS Code.

    ![Ход развертывания в окне вывода VS Code](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. Через несколько минут, когда развертывание завершится (длительность зависит от количества устанавливаемых зависимостей), появится следующее сообщение. Нажмите кнопку **Обзор веб-сайта**, чтобы просмотреть работающий сайт.

    ![Завершенное развертывание с использованием кнопки "Обзор веб-сайта"](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![Приложение успешно запущено в Службе приложений](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. Чтобы проверить развертывание файлов, разверните службу приложений в обозревателе **Azure: Служба приложений**, а затем откройте узел **Файлы**.

    ![Проверка файлов развертывания с помощью обозревателя Службы приложений](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    Служба приложений создает виртуальную среду с указанными зависимостями в папке *antenv*. Развернув этот узел, вы сможете убедиться, что указанные в файле *requirements.txt* пакеты успешно установлены в папку *antenv/lib/python3.7/site-packages*.

> [!div class="nextstepaction"]
> [Приложение развернуто — перейти к шагу 6 >>>](tutorial-deploy-app-service-on-linux-06.md)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)
