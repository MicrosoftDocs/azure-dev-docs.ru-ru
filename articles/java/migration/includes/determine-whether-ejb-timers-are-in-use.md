---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c77a5ae60807cf25415ab86dab9d9891abab13f9
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738413"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Определение того, используются ли таймеры EJB

Если приложение использует таймеры EJB, необходимо убедиться, что код таймера EJB может активироваться каждым экземпляром WildFly независимо друг от друга. Эта проверка необходима, так как в сценарии развертывания Службы Kubernetes Azure каждый таймер EJB будет активироваться в собственном экземпляре WildFly.
