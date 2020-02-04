---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830852"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Определение того, была ли настроена платформа WebLogic

Определите, какие из следующих настроек были сделаны.

* Были ли изменены скрипты запуска? Эти скрипты включают *setDomainEnv*, *commEnv*, *startWebLogic* и *stopWebLogic*.
* Есть ли какие-либо определенные параметры, передаваемые в виртуальную машину Java?
* Есть ли JAR-файлы, включенные в путь к классу сервера?
