---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: d17b69ea5299c24406a63fc6239c350b6cbcbdd4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990146"
---
#### <a name="jms-message-brokers"></a>Брокеры сообщений JMS

Определите брокер или брокеры, которые используются, изучив в манифесте сборки (обычно это файл *pom.xml* или *build.gradle*) соответствующие зависимости.

Например, в приложении Spring Boot, в котором используется ActiveMQ, в файле *pom.xml* обычно присутствует следующая зависимость:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

В приложениях Spring Boot, в которых используются собственные брокеры, зависимости обычно содержатся непосредственно в библиотеках драйверов брокера JMS. Вот пример из файла *build.gradle*:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```
