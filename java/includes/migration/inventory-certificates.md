---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76825903"
---
### <a name="inventory-certificates"></a>Проверка сертификатов

Определите все сертификаты, используемые для общедоступных конечных точек SSL или обмена данными с базами данных внутреннего сервера и другими системами. Вы можете просмотреть все сертификаты на рабочих серверах, выполнив следующую команду:

```bash
keytool -list -v -keystore <path to keystore>
```
