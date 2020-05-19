---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: e9d6993c96fc90c065cda5f8b3177147c35d0242
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990266"
---
Вот пример ActiveMQ из файла *application.properties*:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Дополнительные сведения о конфигурации ActiveMQ см. в [документации по сообщениям Spring Boot](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/boot-features-messaging.html).

Вот пример IBM MQ из файла *application.yaml*:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

Дополнительные сведения о конфигурации IBM MQ см. в [документации по компонентам Spring для IBM MQ](https://github.com/ibm-messaging/mq-jms-spring#ibm-mq-jms-spring-components).
