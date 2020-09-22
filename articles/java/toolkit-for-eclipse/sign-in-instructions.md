---
title: Инструкции по входу для набора средств Azure в Eclipse
description: Узнайте, как войти в Microsoft Azure с помощью набора средств Azure для Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: d0dbc16a16ca3a5ff367e6c67fceabcb37e2cce6
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534431"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Инструкции по входу для набора средств Azure в Eclipse

Набор средств Azure для Eclipse предоставляет два метода для входа в систему с помощью учетной записи Azure:

  - [вход в учетную запись Azure с помощью имени пользователя устройства](#sign-in-to-your-azure-account-by-device-login);
  - [вход в учетную запись Azure с помощью субъекта-службы](#sign-in-to-your-azure-account-by-service-principal).

Предоставляются также методы [**выхода из системы**](#sign-out-of-your-azure-account).

[!INCLUDE [prerequisites](includes/prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Вход в учетную запись Azure с помощью имени пользователя устройства

В этом разделе описывается процесс входа в Azure через устройство.

1. Откройте проект с помощью Eclipse.

1. Выберите **Сервис**, **Azure** и затем **Войти**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Вход в Azure из интегрированной среде разработки Eclipse":::.

1. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

1. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

> [!NOTE]
>
> Если браузер не открывается, настройте Eclipse, чтобы использовать внешний браузер, например Internet Explorer, Firefox или Chrome:
>
> 1. Откройте Preferences > General > Web Browser > Use external web browser in Eclipse (Предпочтения > Общие > Веб-браузер > Использование внешнего веб-браузера в Eclipse).
>
> 2. Выберите предпочитаемый браузер.
>

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

1. Выберите нужную учетную запись Azure и выполните все необходимые для входа процедуры аутентификации.

1. Завершив вход, закройте браузер и вернитесь в интегрированную среду разработки Eclipse. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и щелкните **ОК**.

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Вход в учетную запись Azure с помощью субъекта-службы

В этом разделе показано, как создать файл учетных данных, содержащий данные субъекта-службы. После выполнения этого процесса Eclipse использует файл учетных данных для автоматического входа в Azure при открытии проекта.

1. Откройте проект с помощью Eclipse.

2. Выберите **Сервис**, **Azure** и затем **Войти**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Вход в Azure из интегрированной среде разработки Eclipse":::.

3. В окне **Azure Sign In** (Вход в Azure) выберите **Service Principal** (Субъект-служба). Если у вас еще нет файла проверки подлинности с помощью субъекта-службы, щелкните **New** (Создать), чтобы создать его. В противном случае вы можете щелкнуть **Browse** (Обзор), чтобы открыть нужный файл и перейти к шагу 8.

   ![Окно Azure Sign In (Вход в Azure) с выбранным субъектом-службы][A02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

> [!NOTE]
>
> Если браузер не открывается, настройте Eclipse, чтобы использовать внешний браузер, например IE или Chrome:
>
> 1. Откройте Preferences > General > Web Browser > Use external web browser in Eclipse (Предпочтения > Общие > Веб-браузер > Использование внешнего веб-браузера в Eclipse).
>
> 2. Выберите предпочитаемый браузер.
>

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

6. В диалоговом окне **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Start** (Начать).

7. В диалоговом окне **Service Principal Creation Status** (Состояние создания субъекта-службы) после успешного создания файлов нажмите кнопку **ОК**.

8. Адрес созданного файла будет автоматически заполнен в окне **Azure Sign In** (Вход в Azure). Теперь щелкните **Sign in** (Вход).

   ![Диалоговое окно входа в Azure][A06]

9. Наконец, в диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.


## <a name="sign-out-of-your-azure-account"></a>Выход из учетной записи Azure

После настройки учетной записи путем выполнения действий, описанных в предыдущем разделе, при каждом перезапуске Eclipse будет автоматически выполняться вход. Однако если вы хотите выйти из учетной записи Azure, сделайте следующее:

1. В Eclipse выберите **Сервис**, **Azure** и затем **Выйти**.

2. При отображении диалогового окна **Выход из Azure** нажмите кнопку **Да**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->


<!-- IMG List -->

[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png
[A06]: media/sign-in-instructions/A06.png