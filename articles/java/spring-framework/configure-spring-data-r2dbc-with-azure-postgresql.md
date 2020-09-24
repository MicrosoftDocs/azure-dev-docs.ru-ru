---
title: Использование R2DBC Spring Data с Базой данных Azure для PostgreSQL
description: Узнайте, как использовать R2DBC Spring Data с Базой данных Azure для PostgreSQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: cdda538a986dde8b2a436980298e98a914ef1bb0
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831350"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-postgresql"></a>Использование R2DBC Spring Data с Базой данных Azure для PostgreSQL

В этой статье описано создание примера приложения, которое использует [R2DBC Spring Data](https://spring.io/projects/spring-data-r2dbc) для сохранения данных в [Базе данных Azure для PostgreSQL](/azure/postgresql/) и их извлечения из нее. В этом примере будет использоваться реализация R2DBC для PostgreSQL из репозитория [r2dbc-postgresql](https://github.com/pgjdbc/r2dbc-postgresql) в GitHub.

[R2DBC](https://r2dbc.io/) приносит реактивные API к традиционным реляционным базам данных. Вы можете применять это решение со Spring WebFlux для создания полностью реактивных приложений Spring Boot, которые используют неблокирующие API. Оно обеспечивает улучшенную масштабируемость по сравнению с классическим подходом "один поток на подключение".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-postgresql).

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение в командной строке с помощью следующей команды:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-postgresql-driver-implementation"></a>Добавление реализации реактивного драйвера PostgreSQL

Откройте файл *pom.xml* созданного проекта и добавьте реактивный драйвер PostgreSQL из [репозитория r2dbc-postgresql на GitHub](https://github.com/pgjdbc/r2dbc-postgresql). После зависимости `spring-boot-starter-webflux` добавьте следующий текст:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Настройка Spring Boot для использования Базы данных Azure для PostgreSQL

Откройте файл *src/main/resources/application.properties* и добавьте следующий текст:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:postgres://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_POSTGRESQL_PASSWORD
spring.r2dbc.properties.sslMode=REQUIRE
```

> [!WARNING]
> Из соображений безопасности для Базы данных Azure для PostgreSQL требуется использовать SSL-соединения. Поэтому вам нужно добавить свойство конфигурации `spring.r2dbc.properties.sslMode=REQUIRE`. В противном случае драйвер R2DBC PostgreSQL будет использовать небезопасное подключение, что приведет к сбою.

Замените две переменные `$AZ_DATABASE_NAME` и переменную `$AZ_POSTGRESQL_PASSWORD` значениями, которые вы настроили в начале работы с этой статьей.

> [!NOTE]
> Для лучшей производительности в качестве значения свойства `spring.r2dbc.url` указан пул подключений, как описано в репозитории [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Теперь вы можете запустить приложение с помощью предоставленной программы-оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

[![Выполняющееся приложение](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Создание схемы базы данных

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Остановите приложение и запустите его снова с помощью следующей команды. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана с создаваемой таблицей базы данных:

[![Создание таблицы базы данных](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать R2DBC для хранения и извлечения данных с сервера PostgreSQL.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png#lightbox)

Поздравляем! Вы создали полностью реактивное приложение Spring Boot, которое использует R2DBC для хранения и извлечения данных из Базы данных Azure для PostgreSQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

См. сведения о Spring Data R2DBC в [справочной документации по Spring](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](../index.yml) и [Azure DevOps и Java](/azure/devops/).