---
author: mriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: f99653f3ce30c629c2c11127089445a4c3072a69
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990246"
---
### <a name="inspect-the-deployment-architecture"></a>Инспекция архитектуры развертывания

#### <a name="document-hardware-requirements-for-each-service"></a>Документирование требований к оборудованию для каждой службы

Задокументируйте следующие сведения о приложении Spring Boot:

* Количество выполняемых экземпляров.
* Количество ЦП, выделенных для каждого экземпляра.
* Объем RAM, выделенный для каждого экземпляра.

#### <a name="document-geo-replicationdistribution"></a>Документирование георепликации и распространения

Определите, распределены ли экземпляры приложения Spring Boot между несколькими регионами или центрами обработки данных. Задокументируйте требования к бесперебойной работе или соглашение об уровне обслуживания для приложений, которые вы переносите.
