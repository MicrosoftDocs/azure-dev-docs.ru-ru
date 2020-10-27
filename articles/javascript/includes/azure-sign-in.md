---
ms.custom: devx-track-js
ms.topic: include
ms.date: 010/16/2020
ms.openlocfilehash: fb1c04fe82b67076ca38ef353daf7260544a5881
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179324"
---
Если вы уже используете расширения служб Azure, вы уже вошли в систему и можете пропустить этот шаг. Если вы не используете расширения служб Azure, продолжайте работу с этим разделом, чтобы войти в Azure.

Установив расширение служб Azure в Visual Studio Code, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure** , щелкните **Войти в Azure** и следуйте инструкциям. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

![Вход в Azure с помощью VS Code](../media/deploy-azure/azure-sign-in.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure или состояние "Вход выполнен" отображается в строке состояния, а ваши подписки — в обозревателе **Azure** .

![Строка состояния VS Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar.png)

![Обозреватель Azure VS Code, в котором отображается подписка](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Если отображается сообщение об ошибке **Не удается найти подписку с именем [идентификатор подписки]** , возможно вы находитесь за прокси-сервером и не можете получить доступ к API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера в терминале:
>
> # <a name="bash"></a>[bash](#tab/bash)
>
> ```bash
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> # <a name="powershell"></a>[PowerShell](#tab/powershell)
>
> ```powershell
> $env: HTTPS_PROXY = "https://username:password@proxy:8080"
> $env: HTTP_PROXY = "http://username:password@proxy:8080"
> ```
>
> # <a name="cmd"></a>[Cmd](#tab/cmd)
>
> ```cmd
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ---
