---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: 6bf4c54c8f428e3ffce79a7ba9ac49aae028e2fd
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990236"
---
#### <a name="databases"></a>Базы данных

Определите строки подключения ко всем базам данных SQL.

Для приложений Spring Boot строки подключения обычно хранятся в файлах конфигурации. 

Вот пример из файла *application.properties*:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

Вот пример из файла *application.yml*:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

Дополнительные сценарии настройки см. в документации по Spring Data.

* [Репозитории JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories)
* [Репозитории JDBC](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories)
* [Репозитории Cassandra](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories)
* [Репозитории MongoDB](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongodb.repositories)
