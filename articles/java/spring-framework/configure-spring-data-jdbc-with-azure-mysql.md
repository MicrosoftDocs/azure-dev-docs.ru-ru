---
title: Использование JDBC Spring Data с Базой данных Azure для MySQL
description: Узнайте, как использовать JDBC Spring Data с Базой данных Azure для MySQL.
documentationcenter: java
ms.date: 04/07/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 481b3fce5d7d5f62cf4639d53d86c092e364a74f
ms.sourcegitcommit: 04ee2325e3efd9b7797102b4cd9d5db009c38a42
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2020
ms.locfileid: "86945812"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-mysql"></a>Использование JDBC Spring Data с Базой данных Azure для MySQL

В этой статье описано создание примера приложения, которое использует [JDBC Spring Data](https://spring.io/projects/spring-data-jdbc) для сохранения и извлечения информации в [Базе данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/).

[JDBC](https://jcp.org/en/jsr/detail?id=221) — это стандартный API Java для подключения к стандартным реляционным базам данных.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Пример приложения

В этой статье показано, как создать пример приложения. Если вы хотите ускорить работу, готовое приложение доступно здесь: [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql).

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение, введя в командной строке следующую команду:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Настройка Spring Boot для использования Базы данных Azure для MySQL

Откройте файл *src/main/resources/application.properties* и добавьте следующее:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.datasource.initialization-mode=always
```

- Замените две переменные `$AZ_DATABASE_NAME` значением, которое вы настроили ранее.
- Замените переменную `$AZ_MYSQL_PASSWORD` значением, которое вы настроили ранее.

> [!WARNING]
> Свойство конфигурации `spring.datasource.initialization-mode=always` означает, что Spring Boot при каждом запуске сервера будет автоматически создавать схему базы данных на основе файла `schema.sql`, который вы создадите позднее. Это очень удобно для тестирования, но при каждой перезагрузке все данные будут удаляться, поэтому не следует использовать этот подход в рабочей среде.

> [!NOTE]
> Мы добавляем `?serverTimezone=UTC` к свойству конфигурации `spring.datasource.url`, чтобы драйвер JDBC при соединении с базой данных использовал UTC в качестве формата даты. В противном случае сервер Java и база данных будут использовать разные форматы даты, что неизбежно приведет к ошибке.

Теперь вы можете запустить приложение с помощью предоставленной оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

[![Выполняющееся приложение](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Создание схемы базы данных

Spring Boot будет автоматически выполнять *src/main/resources/`schema.sql`* для создания схемы базы данных. Создайте такой файл со следующим содержимым:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Остановите приложение и запустите его снова. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать JDBC для сохранения и извлечения данных на сервере MySQL.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Ниже приведен снимок экрана с этими запросами cURL:

[![Тестирование с помощью cURL](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png#lightbox)

Поздравляем! Вы создали приложение Spring Boot, которое использует JDBC для сохранения и извлечения данных в Базе данных Azure для MySQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о JDBC для Spring Data см. в [справочной документации по Spring](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference).

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](/azure/developer/java/) и [Azure DevOps и Java](/azure/devops/).
