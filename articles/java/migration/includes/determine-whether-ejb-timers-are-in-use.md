---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672910"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Определение того, используются ли таймеры EJB

Если приложение использует таймеры EJB, необходимо убедиться, что код таймера EJB может активироваться каждым экземпляром WildFly независимо друг от друга. Эта проверка необходима, так как в сценарии развертывания Службы Kubernetes Azure каждый таймер EJB будет активироваться в собственном экземпляре WildFly.
