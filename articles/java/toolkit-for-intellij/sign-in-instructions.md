---
title: Инструкции по входу для набора средств Azure для IntelliJ
description: Сведения о входе в Microsoft Azure с помощью набора средств Azure для IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 27442efda23ba24abe6b40f20f7685f6ac7a524e
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010251"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Инструкции по входу для набора средств Azure для IntelliJ

После [установки](https://www.jetbrains.com/help/idea/managing-plugins.html)[Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) предоставляет два метода для входа в систему с помощью учетной записи Azure:

  - [вход в учетную запись Azure с помощью имени пользователя устройства](#sign-in-to-your-azure-account-by-device-login);
  - [вход в учетную запись Azure с помощью субъекта-службы](#sign-in-to-your-azure-account-by-service-principal).

Предоставляются также методы [**выхода из системы**](#sign-out-of-your-azure-account).

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Вход в учетную запись Azure с помощью имени пользователя устройства

Чтобы войти в Azure с помощью имени пользователя устройства, сделайте следующее:

1. Откройте проект с помощью IntelliJ IDEA.

1. Откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IntelliJ, выберите **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

1. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

1. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

1. Выберите нужную учетную запись Azure и выполните все необходимые для входа процедуры аутентификации.

1. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.


## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Вход в учетную запись Azure с помощью субъекта-службы

В этом разделе показано, как создать файл учетных данных, содержащий данные субъекта-службы. После завершения этого процесса IntelliJ использует файл учетных данных для выполнения автоматического входа в Azure при открытии проекта.

1. Откройте проект с помощью IntelliJ IDEA.

1. Откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IntelliJ, выберите **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

1. В окне **Azure Sign In** (Вход в Azure) выберите **Service Principal** (Субъект-служба) и щелкните **New** (Создать).

   ![Окно Azure Sign In (Вход в Azure) с выбранным субъектом-службы][A02]

1. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

1. Выберите нужную учетную запись Azure и выполните все необходимые для входа процедуры аутентификации. После завершения аутентификации закройте браузер и вернитесь в IntelliJ.

1. В диалоговом окне **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Start** (Начать).

1. В диалоговом окне **Service Principal Creation Status** (Состояние создания субъекта-службы) после успешного создания файлов нажмите кнопку **ОК**.

1. В окне **Azure Sign In** (Вход в Azure) щелкните **Sign in** (Войти). 

1. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   > [!TIP]
   > Создав файл аутентификации субъекта-службы, вы можете перейти к шагу 3, выбрать файл аутентификации и выполнить вход.

## <a name="sign-out-of-your-azure-account"></a>Выход из учетной записи Azure

После настройки учетной записи путем выполнения действий, описанных в предыдущем разделе, при каждом перезапуске IntelliJ будет автоматически выполняться вход. 

Но если вы хотите выйти из своей учетной записи Azure, перейдите к боковой панели Azure Explorer, щелкните значок **Azure Sign Out** (Выход из Azure) или в меню IntelliJ выберите **Tools>Azure>Azure Sign Out** (Средства>Azure>Выход из Azure).


## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png

