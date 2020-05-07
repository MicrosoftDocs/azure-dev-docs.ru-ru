---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672150"
---
### <a name="inventory-certificates"></a>Проверка сертификатов

Определите все сертификаты, используемые для общедоступных конечных точек SSL или обмена данными с базами данных внутреннего сервера и другими системами. Вы можете просмотреть все сертификаты на рабочих серверах, выполнив следующую команду:

```bash
keytool -list -v -keystore <path to keystore>
```
