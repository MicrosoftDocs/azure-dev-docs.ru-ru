---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 26d0422b9ea569b5657a5b7841319e77ce0f1684
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673540"
---
### <a name="determine-whether-session-replication-is-used"></a>Определение того, используется ли репликация сеансов

Если работа приложения зависит от репликации сеансов с помощью модуля Oracle Coherence*Web или без него, у вас есть три варианта:

* Coherence*Web может выполняться параллельно с WebLogic Server на виртуальных машинах Azure, но такой вариант нужно настроить вручную после подготовки предложения. Coherence также может выполняться на виртуальных машинах Azure отдельно, но и этот вариант нужно настроить вручную после подготовки предложения.
* Выполните рефакторинг приложения, чтобы использовать базу данных для управления сеансами.
* Выполните рефакторинг приложения, чтобы перенести сеанс в службу Redis Azure. См. сведения на странице с [ценами на Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

Для всех этих вариантов рекомендуется указать, как именно WebLogic выполняет репликацию состояния сеанса HTTP. См. руководство по [репликации состояния сеанса HTTP](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD) в документации Oracle.
