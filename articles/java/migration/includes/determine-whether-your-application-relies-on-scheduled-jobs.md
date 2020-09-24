---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e4370e6db3589e6050395fe806541505fa3eb8bb
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738161"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или UNIX CronJobs, НЕЛЬЗЯ использовать с Службой Azure Kubernetes. Служба Azure Kubernetes не будет препятствовать развертыванию приложения, содержащего запланированные задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Чтобы выполнить запланированные задания в кластере AKS, определите требуемые задания Kubernetes CronJobs. См. сведения о [выполнении автоматизированных задач с помощью CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
