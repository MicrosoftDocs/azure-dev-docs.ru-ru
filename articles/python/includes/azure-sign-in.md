---
ms.openlocfilehash: cbcc292bf0b59adf35789bbd24ca37b0301a2ad7
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268913"
---
Установив расширение Azure, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure**, выберите **Войти в Azure** и следуйте инструкциям. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

![Вход в Azure с помощью VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure или состояние "Вход выполнен" отображается в строке состояния, а ваши подписки — в обозревателе **Azure**.

![Строка состояния Visual Studio Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

![Обозреватель Службы приложений Azure Visual Studio Code с отображаемой подпиской](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> Если отображается сообщение об ошибке **Не удается найти подписку с именем [идентификатор подписки]** , возможно вы находитесь за прокси-сервером и не можете получить доступ к API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера в терминале:
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
