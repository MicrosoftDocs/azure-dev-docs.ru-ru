---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: f2bc0442aafcfffc963d4e5a6da18085d9205a22
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990166"
---
#### <a name="identify-external-caches"></a>Определение внешних кэшей

Определите все используемые внешние кэши. Redis часто используется через Spring Data Redis. Сведения о конфигурации см. в документации по [Spring Data Redis](https://spring.io/projects/spring-data-redis).

Определите, кэшируются ли данные сеанса с помощью [Spring Session](https://spring.io/projects/spring-session) путем поиска соответствующей конфигурации (в [Java](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-jc) или [XML](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-xml)).
