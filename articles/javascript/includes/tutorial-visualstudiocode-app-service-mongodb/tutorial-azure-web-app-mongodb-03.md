---
title: include file tutorial-azure-web-app-mongodb-03.md
description: include file tutorial-azure-web-app-mongodb-03.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: adea87271b1332f77ab254530410787d1a9baa3c
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183934"
---
В этом разделе руководства показано, как развернуть пример приложения в Azure. Затем вы сможете просмотреть удаленно запущенное приложение в браузере. 

## <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [azure-sign-in](../azure-sign-in.md)]

## <a name="create-web-app-resource"></a>Создание ресурса веб-приложения

Примените расширение Visual Studio Code, чтобы создать ресурс Службы приложений и развернуть веб-приложение в этом ресурсе.

1. Перейдите в обозреватель Azure. Щелкните подписку правой кнопкой мыши и выберите действие `Create new web app...`.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Снимок части экрана Visual Studio Code, на котором показано, как расширение Службы приложений Azure применяется для создания веб-приложения.":::

1. Выполните инструкции из следующей таблицы, чтобы разобраться в применении значений разных свойств.

    |Свойство|Значение|
    |--|--|
    |Введите глобально уникальное имя для нового веб-приложения.| Введите значение для ресурса Службы приложений, например `web-app-with-mongodb-YOUR-NAME`. Замените строку `<YOUR-NAME>` именем или уникальным идентификатором. Это уникальное имя также добавляется в URL-адрес для доступа к этому ресурсу в браузере.|
    |Выберите среду выполнения для приложения Linux.|Выберите `Node 12 LTS`.|

1. Когда процесс создания приложения завершится, в нижнем правом углу Visual Studio Code отобразится сообщение о состоянии с возможностью выбрать действие `Deploy` или `View output`. Выберите `Deploy`.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Снимок части экрана Visual Studio Code, на котором показано, как расширение Службы приложений Azure применяется для создания веб-приложения.":::

    Если сообщение о состоянии исчезнет, для запуска развертывания откройте обозреватель Azure и щелкните правой кнопкой мыши имя ресурса, а затем выберите пункт **Развертывание в виде веб-приложения...** .

1. В процессе развертывания вы увидите уведомление, которое позволяет открыть **окно вывода** .  В нем отображается пошаговое состояние развертывания. 

1. Когда развертывание успешно завершится, вы увидите соответствующее уведомление. Щелкните **Журналы потоковой передачи** , чтобы просмотреть текущие журналы. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="Снимок части экрана Visual Studio Code, на котором показано, как расширение Службы приложений Azure применяется для создания веб-приложения.":::

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="Снимок части экрана Visual Studio Code, на котором показано, как расширение Службы приложений Azure применяется для создания веб-приложения.":::    

1. Откройте веб-сайт в браузере и замените текст `YOUR-RESOURCE_NAME` именем своего ресурса: `https://YOUR-RESOURCE_NAME.azurewebsites.net`.
    
    Теперь этот веб-сайт может работать как локально, так и удаленно, но пока не может подключиться к базе данных. 

## <a name="want-to-know-more"></a>Хотите получить дополнительные сведения?

Начальная версия веб-службы настроена на работу через порт 8080 и является общедоступной. Вы можете изменить эти параметры для своего веб-сайта.
* [Параметры приложения](/azure/app-service/configure-common)
* [Аутентификация](/azure/app-service/configure-authentication-provider-microsoft)
* [Ограничение доступа в зависимости от сети](/azure/azure/app-service/app-service-ip-restrictions)

Если вы используете это расширение Службы приложений для развертывания веб-сайта в облаке Azure, вам будет полезно получить сведения о [настройке такого развертывания.](https://github.com/microsoft/vscode-azureappservice/wiki/Configuring-Zip-Deployment#additional-zip-deploy-configuration-settings)