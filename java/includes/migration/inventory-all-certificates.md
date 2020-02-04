---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830802"
---
### <a name="inventory-all-certificates"></a>Проверка всех сертификатов

Определите все сертификаты, используемые для общедоступных конечных точек SSL. Вы можете просмотреть все сертификаты на рабочих серверах, выполнив следующую команду:

```bash
keytool -list -v -keystore <path to keystore>
```
