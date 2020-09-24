---
author: mnriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: 82cf9553654e1efc884542ecdd52d294688291df
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738169"
---
### <a name="inspect-the-deployment-architecture"></a>Инспекция архитектуры развертывания

#### <a name="document-hardware-requirements-for-each-service"></a>Документирование требований к оборудованию для каждой службы

Задокументируйте следующие сведения о приложении Spring Boot:

* Количество выполняемых экземпляров.
* Количество ЦП, выделенных для каждого экземпляра.
* Объем RAM, выделенный для каждого экземпляра.

#### <a name="document-geo-replicationdistribution"></a>Документирование георепликации и распространения

Определите, распределены ли экземпляры приложения Spring Boot между несколькими регионами или центрами обработки данных. Задокументируйте требования к бесперебойной работе или соглашение об уровне обслуживания для приложений, которые вы переносите.
