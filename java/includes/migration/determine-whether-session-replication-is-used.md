---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 4c20feba0a91d10846963f8697457735e5932c20
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830732"
---
### <a name="determine-whether-session-replication-is-used"></a>Определение того, используется ли репликация сеансов

Если работа приложения зависит от репликации сеансов с помощью модуля Oracle Coherence*Web или без него, у вас есть три варианта:

1. Coherence*Web может выполняться параллельно с WebLogic Server на виртуальных машинах Azure, но такой вариант нужно настроить вручную после подготовки предложения. Coherence также может выполняться на виртуальных машинах Azure отдельно, но и этот вариант нужно настроить вручную после подготовки предложения.
2. Выполните рефакторинг приложения, чтобы использовать базу данных для управления сеансами.
3. Выполните рефакторинг приложения, чтобы перенести сеанс в службу Redis Azure. См. сведения на странице с [ценами на Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

Для всех этих вариантов рекомендуется указать, как именно WebLogic выполняет репликацию состояния сеанса HTTP. См. руководство по [репликации состояния сеанса HTTP](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD) в документации Oracle.
