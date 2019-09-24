---
ms.openlocfilehash: d64a5c908915b5d020f27e1c501aee852f04ae38
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019662"
---
Установив расширение Azure, войдите в свою учетную запись Azure, перейдя в обозреватель **Azure: Служба приложений**, щелкните **Войти в Azure** и следуйте инструкциям на экране.

![Вход в Azure с помощью VS Code](../media/deploy-azure/azure-sign-in.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure отображается в строке состояния, а ваши подписки — в обозревателе **Azure: Служба приложений**.

![Строка состояния VS Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar.png)

![Обозреватель Службы приложений Azure VS Code, в котором отображается подписка](../media/deploy-azure/azure-subscription-view.png)

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
