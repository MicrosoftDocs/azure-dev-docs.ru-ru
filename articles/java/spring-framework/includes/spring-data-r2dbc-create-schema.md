---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: a94dbf3d29863b1a4ac6909db839c451bbf482fd
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018572"
---
В основном классе `DemoApplication` настройте новый bean-компонент Spring, который создаст схему базы данных, с помощью следующего кода:

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.core.io.ClassPathResource;
import org.springframework.data.r2dbc.connectionfactory.init.ConnectionFactoryInitializer;
import org.springframework.data.r2dbc.connectionfactory.init.ResourceDatabasePopulator;

import io.r2dbc.spi.ConnectionFactory;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
}
```

Этот bean-компонент Spring использует файл с именем *schema.sql*, поэтому создайте этот файл в папке *src/main/resources* и добавьте следующий текст:
