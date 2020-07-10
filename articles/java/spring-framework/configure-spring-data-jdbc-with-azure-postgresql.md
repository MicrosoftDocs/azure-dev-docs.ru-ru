---
title: Использование JDBC Spring Data с Базой данных Azure для PostgreSQL
description: Сведения о том, как использовать JDBC Spring Data с Базой данных Azure для PostgreSQL.
documentationcenter: java
ms.date: 05/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 91cb38146b123f8d05eb9ca89b88af9e776f74f6
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018520"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-postgresql"></a>Использование JDBC Spring Data с Базой данных Azure для PostgreSQL

В этой статье описано создание примера приложения, которое использует [JDBC Spring Data](https://spring.io/projects/spring-data-jdbc) для сохранения данных в [Базе данных Azure для PostgreSQL](/azure/postgresql/) и их извлечения из нее.

[JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) — это стандартный API Java для подключения к стандартным реляционным базам данных.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql).

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение в командной строке с помощью следующей команды:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,postgresql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Настройка Spring Boot для использования Базы данных Azure для PostgreSQL

Откройте файл *src/main/resources/application.properties* и добавьте следующий текст:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_POSTGRESQL_PASSWORD

spring.datasource.initialization-mode=always
```

Замените две переменные `$AZ_DATABASE_NAME` и переменную `$AZ_POSTGRESQL_PASSWORD` значениями, которые вы настроили в начале работы с этой статьей.

> [!WARNING]
> Свойство конфигурации `spring.datasource.initialization-mode=always` означает, что Spring Boot при каждом запуске сервера будет автоматически создавать схему базы данных на основе файла *schema.sql*, который вы создадите позднее. Это очень удобно для тестирования, но при каждой перезагрузке все данные будут удаляться, поэтому не следует использовать этот подход в рабочей среде.

Теперь вы можете запустить приложение с помощью предоставленной программы-оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

[![Выполняющееся приложение](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Создание схемы базы данных

Spring Boot будет автоматически выполнять файл *src/main/resources/schema.sql* для создания схемы базы данных. Создайте файл и добавьте следующее содержимое:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Остановите приложение и запустите его снова с помощью следующей команды. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать JDBC для хранения данных на сервере PostgreSQL и их извлечения из него.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

Поздравляем! Вы создали приложение Spring Boot, которое использует JDBC для сохранения данных в Базе данных Azure для PostgreSQL и их извлечения из нее.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о JDBC для Spring Data см. в [справочной документации по Spring](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](/azure/developer/java/) и [Azure DevOps и Java](/azure/devops/).
