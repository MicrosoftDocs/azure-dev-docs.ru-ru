---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: fb2311fe9256daa53b2687c43b508c4af4f97bd2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401691"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Создание проекта Firebase и включение Firebase Cloud Messaging для Android

1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase: введите **PushDemo** в качестве **названия проекта**.

    > [!NOTE]
    > Для вас будет автоматически сгенерировано уникальное название. По умолчанию оно состоит из варианта указанного вами названия в нижнем регистре, дефиса и автоматически сгенерированного числа. Вы можете изменить это название при условии, что оно останется глобально уникальным.

1. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android).

    ![Добавление Firebase в приложение Android](../media/notification-hubs-add-firebase-to-android-app.png)

1. На странице **Добавление Firebase в приложение для Android** выполните следующие действия:
    1. В поле **Android package name** (Имя пакета Android) введите имя пакета. Например: `com.<organization_identifier>.<package_name>`.

        ![Указание имени пакета.](../media/specify-package-name-firebase-cloud-messaging-settings.png)
    1. Выберите **Регистрация приложения**.  
    1. Выберите **Download google-services.json** (Скачать Download google-services.json). Затем сохраните файл в локальную папку для дальнейшего использования и выберите **Далее**.  

        ![Загрузка файла google-services.json.](../media/download-google-service-button.png)
    1. Выберите **Далее**.
    1. Щелкните **Открыть консоль**.

        > [!NOTE]
        > Если кнопка **Открыть консоль** неактивна из-за *проверки установки*, выберите **Пропустить**.

1. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](../media/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Если вы еще не скачали файл **google-services.json**, вы можете скачать его на этой странице.

1. Переключитесь на вкладку **Обмен сообщениями в облаке** в верхней части. Скопируйте и сохраните **Ключ сервера** для последующего использования. Это значение будет использоваться для настройки концентратора уведомлений.

    ![Копирование ключа сервера](../media/server-key.png)
