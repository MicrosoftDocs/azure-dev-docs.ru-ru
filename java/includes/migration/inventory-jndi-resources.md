---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 171dac6ad7e2761d58f63d173cc78464cedb6dc5
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830812"
---
### <a name="inventory-jndi-resources"></a>Проверка ресурсов JNDI

Проверьте все ресурсы JNDI. Например, у источников данных, таких как базы данных, может быть связанное имя JNDI, которое позволяет JPA правильно привязывать экземпляры `EntityManager` к определенной базе данных. См. сведения о ресурсах и базах данных JNDI в описании [источников данных WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html) в документации по Oracle. Для других ресурсов, связанных с JNDI, таких как брокеры сообщений JMS, может потребоваться выполнить миграцию или перенастройку. См. сведения о [конфигурации ресурсов JMS](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/jmsad/overview.html).
