---
ms.openlocfilehash: 90ac4162e1a6032bcb1f3cfab7f249e4538314f7
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441479"
---
Установив расширение Azure, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure**, выберите **Войти в Azure** и следуйте инструкциям. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

![Вход в Azure с помощью VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure или состояние "Вход выполнен" отображается в строке состояния, а ваши подписки — в обозревателе **Azure**.

![Строка состояния Visual Studio Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

![Обозреватель Службы приложений Azure Visual Studio Code с отображаемой подпиской](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> Если отображается сообщение об ошибке **Не удается найти подписку с именем [идентификатор подписки]** , возможно вы находитесь за прокси-сервером и не можете получить доступ к API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера в терминале:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```