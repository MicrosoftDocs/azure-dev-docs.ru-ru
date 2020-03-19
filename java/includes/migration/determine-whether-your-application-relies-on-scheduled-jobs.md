---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 4d2df28d5c752d20454c28a6d4a53698aa7ff5b6
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897442"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или UNIX CronJobs, НЕЛЬЗЯ использовать с Службой Azure Kubernetes. Служба Azure Kubernetes не будет препятствовать развертыванию приложения, содержащего запланированные задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Чтобы выполнить запланированные задания в кластере AKS, определите требуемые задания Kubernetes CronJobs. См. сведения о [выполнении автоматизированных задач с помощью CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
