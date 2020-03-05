---
ms.openlocfilehash: c9d46571c22c9bee49352dd4c1f492b2c9ff60a8
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709839"
---
Установив расширение Azure, войдите в свою учетную запись Azure. Для этого перейдите в обозреватель **Azure**, выберите **Войти в Azure** и следуйте инструкциям. (Если вы установили несколько расширений Azure, выберите одно для области, в которой работаете, например Службы приложений, Функций и т. д.).

![Вход в Azure с помощью VS Code](../media/deploy-azure/azure-sign-in.png)

Выполнив вход, убедитесь, что адрес электронной почты учетной записи Azure или состояние "Вход выполнен" отображается в строке состояния, а ваши подписки — в обозревателе **Azure**.

![Строка состояния VS Code, в которой отображается учетная запись Azure](../media/deploy-azure/azure-account-status-bar.png)

![Обозреватель Azure VS Code, в котором отображается подписка](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Если отображается сообщение об ошибке **Не удается найти подписку с именем [идентификатор подписки]** , возможно вы находитесь за прокси-сервером и не можете получить доступ к API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера в терминале:
>
> ```bash
> # MacOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
