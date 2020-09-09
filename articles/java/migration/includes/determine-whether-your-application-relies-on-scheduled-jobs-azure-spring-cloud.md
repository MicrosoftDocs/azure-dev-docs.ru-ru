---
author: yevster
ms.author: yebronsh
ms.date: 7/21/2020
ms.openlocfilehash: b1acff280bddea700b0382609c1003129b05dad2
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062652"
---
#### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или UNIX CronJobs, НЕЛЬЗЯ использовать с Azure Spring Cloud. Azure Spring Cloud не будет препятствовать развертыванию приложения, содержащего запланированные задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Проверьте все запланированные задачи, выполняемые на рабочих серверах, внутри или за пределами кода приложения.
