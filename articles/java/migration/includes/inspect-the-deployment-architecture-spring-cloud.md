---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 7b2ad87dfe2e2c7358737ff02ec52b97b1332ae7
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990136"
---
### <a name="inspect-the-deployment-architecture"></a>Инспекция архитектуры развертывания

#### <a name="document-hardware-requirements-for-each-service"></a>Документирование требований к оборудованию для каждой службы

Для каждой службы Spring Cloud (не включая сервер конфигурации, реестр или шлюз) задокументируйте следующие сведения:

* Количество выполняемых экземпляров.
* Количество ЦП, выделенных для каждого экземпляра.
* Объем RAM, выделенный для каждого экземпляра.

#### <a name="document-geo-replicationdistribution"></a>Документирование георепликации и распространения

Определите, распределены ли приложения Spring Boot между несколькими регионами или центрами обработки данных. Задокументируйте требования к бесперебойной работе или соглашение об уровне обслуживания для приложений, которые вы переносите.

#### <a name="identify-clients-that-bypass-the-service-registry"></a>Выявление клиентов, которые обходят реестр службы

Определите все клиентские приложения, которые вызывают любую службу, которую нужно перенести, не используя при этом реестр службы Spring Cloud. После миграции такие вызовы станут невозможны. Перед миграцией настройте такие клиенты на использование [Spring Cloud OpenFeign](https://spring.io/projects/spring-cloud-openfeign).
