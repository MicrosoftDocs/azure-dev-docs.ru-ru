---
title: Инструкции по входу для набора средств Azure для IntelliJ
description: Сведения о входе в Microsoft Azure с помощью набора средств Azure для IntelliJ.
services: ''
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 02/01/2018
ms.devlang: Java
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: na
ms.openlocfilehash: 7c2cec950c8a8bd51a7e1c6f9e5de390e1799c15
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68278865"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Инструкции по входу для набора средств Azure для IntelliJ

После [установки](https://www.jetbrains.com/help/idea/managing-plugins.html) [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) предоставляет два метода для входа в систему с помощью учетной записи Azure:

  - [вход в учетную запись Azure с помощью имени пользователя устройства](#sign-in-to-your-azure-account-by-device-login);
  - [вход в учетную запись Azure с помощью субъекта-службы](#sign-in-to-your-azure-account-by-service-principal).

Предоставляются также методы [**выхода из системы**](#sign-out-of-your-azure-account).

[!INCLUDE [azure-toolkit-for-intellij-basic-prerequisites](../includes/azure-toolkit-for-intellij-basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Вход в учетную запись Azure с помощью имени пользователя устройства

Чтобы войти в Azure с помощью имени пользователя устройства, сделайте следующее:

1. Откройте проект с помощью IntelliJ IDEA.

2. Откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IDEA **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][I03]

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][I04]

6. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Вход в учетную запись Azure с помощью субъекта-службы

В этом разделе показано, как создать файл учетных данных, содержащий данные субъекта-службы. После выполнения этого процесса IntelliJ использует файл учетных данных для автоматического входа в Azure при открытии проекта.

1. Откройте проект с помощью IntelliJ IDEA.

1. Откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IDEA **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).
   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][A01]

1. В окне **Azure Sign In** (Вход в Azure) выберите **Service Principal** (Субъект-служба) и щелкните **New** (Создать).

   ![Окно Azure Sign In (Вход в Azure) с выбранным субъектом-службы][A02]

1. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][A03]

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][A04]

1. В диалоговом окне **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Start** (Начать).

   ![Окно Create authentication files (Создание файлов проверки подлинности)][A05]

1. В диалоговом окне **Service Principal Creation Status** (Состояние создания субъекта-службы) после успешного создания файлов нажмите кнопку **ОК**.

   ![Диалоговое окно Service Principal Creation Status (Состояние создания субъекта-службы)][A06]

1. В окне **Azure Sign In** (Вход в Azure) щелкните **Sign in** (Войти). 

   ![Диалоговое окно входа в Azure][A07]

1. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A08]

> Создав файл проверки подлинности субъекта-службы, вы можете перейти к шагу 8, выбрать файл проверки подлинности и выполнить вход.

## <a name="sign-out-of-your-azure-account"></a>Выход из учетной записи Azure

После настройки учетной записи путем выполнения действий, описанных в предыдущем разделе, при каждом перезапуске IntelliJ будет автоматически выполняться вход. Но если вы хотите выйти из учетной записи Azure, сделайте следующее:

* В IntelliJ IDEA откройте боковую панель Azure Explorer, щелкните **Azure Sign Out** (Выйти из Azure) (или в меню IDEA **Tools/Azure/Azure Sign in** (Средства/Azure/Выход из Azure)).

   ![Команда Azure Sign Out (Выход из Azure) в IntelliJ][L01]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-intellij-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-intellij-sign-in-instructions/I05.png

[A01]: media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: media/azure-toolkit-for-intellij-sign-in-instructions/A08.png
[A09]: media/azure-toolkit-for-intellij-sign-in-instructions/A09.png

[L01]: media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: media/azure-toolkit-for-intellij-sign-in-instructions/L03.png
