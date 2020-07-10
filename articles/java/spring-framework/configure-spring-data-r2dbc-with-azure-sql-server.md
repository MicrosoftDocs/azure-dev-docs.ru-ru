---
title: Использование R2DBC Spring Data с Базой данных SQL Azure
description: Узнайте, как использовать R2DBC Spring Data с Базой данных SQL Azure.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 64316322d509d588f94185452d5c21fdee6e9ef1
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018652"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Использование R2DBC Spring Data с Базой данных SQL Azure

В этом разделе показано, как создать пример приложения для хранения информации в [Базе данных SQL](https://docs.microsoft.com/azure/sql-database/) и ее извлечения с помощью [R2DBC Spring Data](https://spring.io/projects/spring-data-r2dbc), используя реализацию R2DBC для Microsoft SQL Server из [репозитория GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

[R2DBC](https://r2dbc.io/) приносит реактивные API к традиционным реляционным базам данных. Вы можете применять это решение со Spring WebFlux для создания полностью реактивных приложений Spring Boot, которые используют неблокирующие API. Оно обеспечивает улучшенную масштабируемость по сравнению с классическим подходом "один поток на подключение".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server).

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение в командной строке с помощью следующей команды:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Добавление реализации реактивного драйвера Базы данных SQL Azure

Откройте файл *pom.xml* в созданном проекте, чтобы добавить реактивный драйвер Базы данных SQL Azure из [репозитория GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

После зависимости `spring-boot-starter-webflux` добавьте следующий текст:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Настройка Spring Boot для использования Базы данных SQL Azure

Откройте файл *src/main/resources/application.properties* и добавьте следующий текст:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

Замените две переменные `$AZ_DATABASE_NAME` и переменную `$AZ_SQL_SERVER_PASSWORD` значениями, которые вы настроили в начале работы с этой статьей.

> [!NOTE]
> Для лучшей производительности в качестве значения свойства `spring.r2dbc.url` указан пул подключений, как описано в репозитории [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Теперь вы можете запустить приложение с помощью предоставленной программы-оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

[![Выполняющееся приложение](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Создание схемы базы данных

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

Остановите приложение и запустите его снова с помощью следующей команды. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана с создаваемой таблицей базы данных:

[![Создание таблицы базы данных](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png#lightbox)

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать R2DBC для хранения и извлечения данных из сервера Базы данных SQL Azure.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png#lightbox)

Поздравляем! Вы создали полностью реактивное приложение Spring Boot, которое использует R2DBC для хранения и извлечения данных из Базы данных SQL Azure.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

См. сведения о Spring Data R2DBC в [справочной документации по Spring](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](/azure/developer/java/) и [Azure DevOps и Java](/azure/devops/).
