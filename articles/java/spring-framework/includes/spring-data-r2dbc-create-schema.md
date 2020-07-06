---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: fa66c4e9db481e31853c8e67816a14b6ee753fd2
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507523"
---
В основном классе `DemoApplication` настройте новый bean-компонент Spring, который создаст схему базы данных, с помощью следующего кода:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Этот bean-компонент Spring использует файл с именем *schema.sql*, поэтому создайте этот файл в папке *src/main/resources* и добавьте следующий текст:
