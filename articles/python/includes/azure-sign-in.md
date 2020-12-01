---
ms.openlocfilehash: c78b25e2111e48ed9337bcd85f6585c4bdd4a081
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035521"
---
Установив расширение Azure, войдите в свою учетную запись Azure:

1. Перейдите в обозреватель **Azure**.
1. Выберите элемент **Войти в Azure** и следуйте подсказкам. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

    ![Вход в Azure с помощью VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

1. После входа убедитесь, что в строке состояния указано **Azure: вход выполнен** и ваши подписки отображаются в обозревателе **Azure**.

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
