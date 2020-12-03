---
title: Использование JPA Spring Data с Базой данных SQL Azure
description: Сведения о том, как использовать JPA Spring Data с базой данных SQL Azure.
documentationcenter: java
ms.date: 10/14/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 8483df505bfeda0c04ead0c2d12e941a5f4ba6d8
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442265"
---
# <a name="use-spring-data-jpa-with-azure-sql-database"></a>Использование JPA Spring Data с Базой данных SQL Azure

В этой статье описано создание примера приложения, которое использует [JPA Spring Data](https://spring.io/projects/spring-data-jpa) для сохранения и извлечения информации в [Базе данных SQL Azure](/azure/sql-database/).

[Java Persistence API (JPA)](https://en.wikipedia.org/wiki/Java_Persistence_API) — это стандартный API Java для объектно-реляционного сопоставления.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-jpa-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-jpa-sql-server).

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение, введя в командной строке следующую команду:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,sqlserver -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```


### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Настройка Spring Boot для использования Базы данных SQL Azure

Откройте файл *src/main/resources/application.properties* и добавьте следующее содержимое. Обязательно замените две переменные `$AZ_DATABASE_NAME` и переменную `$AZ_SQL_SERVER_PASSWORD` значениями, которые вы настроили в начале работы с этой статьей.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_SQL_SERVER_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> Свойство конфигурации `spring.jpa.hibernate.ddl-auto=create-drop` означает, что Spring Boot будет автоматически создавать схему данных при запуске приложения и попытается удалить ее после завершения работы приложения. Это очень удобно для тестирования, но не используйте его в производственной среде!

Теперь вы можете запустить приложение с помощью предоставленной оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

[![Выполняющееся приложение](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-01.png)](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-01.png#lightbox)

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать JPA для хранения данных в Базе данных SQL Azure и их извлечения из нее.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-02.png)](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-02.png#lightbox)

Поздравляем! Вы создали приложение Spring Boot, которое использует JPA для сохранения данных в Базе данных SQL Azure и их извлечения из нее.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о JPA для Spring Data см. в [справочной документации по Spring](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](../index.yml) и [Azure DevOps и Java](/azure/devops/).