---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673160"
---
### <a name="inventory-all-certificates"></a>Проверка всех сертификатов

Определите все сертификаты, используемые для общедоступных конечных точек SSL. Вы можете просмотреть все сертификаты на рабочих серверах, выполнив следующую команду:

```bash
keytool -list -v -keystore <path to keystore>
```
