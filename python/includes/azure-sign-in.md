---
ms.openlocfilehash: fcbce1c0f17721c7f3fd90e8d396baba6ae100c4
ms.sourcegitcommit: 6012460ad8d6ff112226b8f9ea6da397ef77712d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72278832"
---
Установив расширение Azure, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure**, выберите **Войти в Azure** и следуйте инструкциям. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

![Вход в Azure с помощью VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure или состояние "Вход выполнен" отображается в строке состояния, а ваши подписки — в обозревателе **Azure**.

![Строка состояния Visual Studio Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

![Обозреватель Службы приложений Azure Visual Studio Code с отображаемой подпиской](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> Если отображается сообщение об ошибке **Не удается найти подписку с именем [идентификатор подписки]** , возможно вы находитесь за прокси-сервером и не можете получить доступ к API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера в терминале:
>
> ```sh
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
>
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
