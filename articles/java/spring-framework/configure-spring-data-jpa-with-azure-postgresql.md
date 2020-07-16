---
title: Использование JPA Spring Data с Базой данных Azure для PostgreSQL
description: Сведения о том, как использовать JPA Spring Data с Базой данных Azure для PostgreSQL.
documentationcenter: java
ms.date: 06/19/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 17487e2440d42bc048a7058183e924febe0a0d33
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378868"
---
# <a name="use-spring-data-jpa-with-azure-database-for-postgresql"></a>Использование JPA Spring Data с Базой данных Azure для PostgreSQL

В этой статье описано создание примера приложения, которое использует [JPA Spring Data](https://spring.io/projects/spring-data-jpa) для сохранения данных в [Базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) и их извлечения из нее.

[Java Persistence API (JPA)](https://en.wikipedia.org/wiki/Java_Persistence_API) — это стандартный API Java для объектно-реляционного сопоставления.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-jpa-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-jpa-postgresql).

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение, введя в командной строке следующую команду:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,postgresql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Настройка Spring Boot для использования Базы данных Azure для PostgreSQL

Откройте файл *src/main/resources/application.properties* и добавьте следующее содержимое. Обязательно замените две переменные `$AZ_DATABASE_NAME` и переменную `$AZ_POSTGRESQL_PASSWORD` значениями, которые вы настроили в начале работы с этой статьей.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_POSTGRESQL_PASSWORD

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

[![Выполняющееся приложение](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-01.png#lightbox)

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать JPA для хранения данных на сервере PostgreSQL и их извлечения из него.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-02.png#lightbox)

Поздравляем! Вы создали приложение Spring Boot, которое использует JPA для сохранения данных в Базе данных Azure для PostgreSQL и их извлечения из нее.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о JPA для Spring Data см. в [справочной документации по Spring](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](/azure/developer/java/) и [Azure DevOps и Java](/azure/devops/).
