---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 430f4fef9c512f91da4fd95f04320ddd127ea784
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990256"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Источники и секреты конфигурации инвентаризации

#### <a name="inventory-passwords-and-secure-strings"></a>Пароли и защищенные строки инвентаризации

Проверьте наличие секретных строк и паролей во всех свойствах и файлах конфигурации, а также всех переменных среды в рабочих развертываниях. В приложении Spring Boot такие строки обычно содержатся в файле *application.properties* или *application.yml*.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]
