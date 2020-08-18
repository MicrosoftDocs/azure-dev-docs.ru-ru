---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: f50115be65be8e746527b3d4ee833a738109ddbc
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052267"
---
### <a name="determine-whether-weblogic-clustering-is-used"></a>Определение того, используется ли кластеризация WebLogic

Скорее всего, вы развернули приложение на нескольких серверах WebLogic Server для обеспечения высокой доступности. Вы можете перенести эти кластеры непосредственно из локальной установки на платформу WebLogic, работающую в Виртуальных машинах Azure. См. сведения о [файлах конфигурации домена](https://docs.oracle.com/middleware/12213/wls/DOMCF/config_files.htm#DOMCF127) в документации по Oracle.

### <a name="account-for-load-balancing-requirements"></a>Учет требований к балансировке нагрузки

Балансировка нагрузки является важнейшей частью процесса переноса кластера Oracle WebLogic Server в Azure.  Самым простым решением является использование встроенной поддержки [Шлюза приложений Azure](/azure/application-gateway/overview), предоставляемой в предложении Azure Marketplace для кластера Oracle WebLogic Server.  Дополнительные сведения см. в статье [Учебник. Перенос кластера серверов WebLogic Server в Azure с помощью Шлюза приложений Azure в качестве подсистемы балансировки нагрузки](../migrate-weblogic-with-app-gateway.md).

Сводные сведения о возможностях Шлюза приложений Azure по сравнению с другими решениями для балансировки нагрузки Azure см. в статье [Общие сведения о параметрах балансировки нагрузки в Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).
