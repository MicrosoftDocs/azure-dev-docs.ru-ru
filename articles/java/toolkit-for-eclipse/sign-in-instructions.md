---
title: Инструкции по входу для набора средств Azure в Eclipse
description: Узнайте, как войти в Microsoft Azure с помощью набора средств Azure для Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: accae3c9ab06eebe295aa745c62c4eff542d02d4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81674550"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Инструкции по входу для набора средств Azure в Eclipse

Набор средств Azure для Eclipse предоставляет два метода для входа в систему с помощью учетной записи Azure:

  - [вход в учетную запись Azure с помощью имени пользователя устройства](#sign-in-to-your-azure-account-by-device-login);
  - [вход в учетную запись Azure с помощью субъекта-службы](#sign-in-to-your-azure-account-by-service-principal).

Предоставляются также методы [**выхода из системы**](#sign-out-of-your-azure-account).

[!INCLUDE [prerequisites](includes/prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Вход в учетную запись Azure с помощью имени пользователя устройства

Чтобы войти в Azure с помощью имени пользователя устройства, сделайте следующее:

1. Откройте проект с помощью Eclipse.

2. Выберите **Сервис**, **Azure** и затем **Войти**.
   ![Меню Eclipse для входа в систему Azure][I01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][I03]

> [!NOTE]
>
> Если браузер не открывается, настройте Eclipse, чтобы использовать внешний браузер, например Internet Explorer, Firefox или Chrome:
>
> 1. Откройте Preferences > General > Web Browser > Use external web browser in Eclipse (Предпочтения > Общие > Веб-браузер > Использование внешнего веб-браузера в Eclipse).
>
> 2. Выберите предпочитаемый браузер.
>

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][I04]

6. Наконец, в диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Вход в учетную запись Azure с помощью субъекта-службы

В этом разделе показано, как создать файл учетных данных, содержащий данные субъекта-службы. После выполнения этого процесса Eclipse использует файл учетных данных для автоматического входа в Azure при открытии проекта.

1. Откройте проект с помощью Eclipse.

2. Выберите **Сервис**, **Azure** и затем **Войти**.
   ![Команда Azure Sign In (Вход в Azure) в Eclipse][A01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Service Principal** (Субъект-служба). Если у вас еще нет файла проверки подлинности с помощью субъекта-службы, щелкните **New** (Создать), чтобы создать его. В противном случае вы можете щелкнуть **Browse** (Обзор), чтобы открыть нужный файл и перейти к шагу 8.

   ![Окно Azure Sign In (Вход в Azure) с выбранным субъектом-службы][A02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][A08]

> [!NOTE]
>
> Если браузер не открывается, настройте Eclipse, чтобы использовать внешний браузер, например IE или Chrome:
>
> 1. Откройте Preferences > General > Web Browser > Use external web browser in Eclipse (Предпочтения > Общие > Веб-браузер > Использование внешнего веб-браузера в Eclipse).
>
> 2. Выберите предпочитаемый браузер.
>

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][A03]

6. В диалоговом окне **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Start** (Начать).

   ![Окно Create authentication files (Создание файлов проверки подлинности)][A04]

7. В диалоговом окне **Service Principal Creation Status** (Состояние создания субъекта-службы) после успешного создания файлов нажмите кнопку **ОК**.

   ![Диалоговое окно Service Principal Creation Status (Состояние создания субъекта-службы)][A05]

8. Адрес созданного файла будет автоматически заполнен в окне **Azure Sign In** (Вход в Azure). Теперь щелкните **Sign in** (Вход).

   ![Диалоговое окно входа в Azure][A06]

9. Наконец, в диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A07]

## <a name="sign-out-of-your-azure-account"></a>Выход из учетной записи Azure

После настройки учетной записи путем выполнения действий, описанных в предыдущем разделе, при каждом перезапуске Eclipse будет автоматически выполняться вход. Однако если вы хотите выйти из учетной записи Azure, сделайте следующее:

1. В Eclipse выберите **Сервис**, **Azure** и затем **Выйти**.

   ![Меню Eclipse для выхода из системы Azure][L01]

2. При отображении диалогового окна **Выход из Azure** нажмите кнопку **Да**.

   ![Диалоговое окно выхода][L02]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->


<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[A01]: media/sign-in-instructions/A01.png
[A02]: media/sign-in-instructions/A02.png
[A03]: media/sign-in-instructions/A03.png
[A04]: media/sign-in-instructions/A04.png
[A05]: media/sign-in-instructions/A05.png
[A06]: media/sign-in-instructions/A06.png
[A07]: media/sign-in-instructions/A07.png
[A08]: media/sign-in-instructions/A08.png

[L01]: media/sign-in-instructions/L01.png
[L02]: media/sign-in-instructions/L02.png
[L03]: media/sign-in-instructions/L03.png
