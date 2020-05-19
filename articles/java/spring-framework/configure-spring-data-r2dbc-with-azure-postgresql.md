---
title: Использование R2DBC Spring Data с Базой данных Azure для PostgreSQL
description: Узнайте, как использовать R2DBC Spring Data с Базой данных Azure для PostgreSQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 11de1a86b84a0369253d7be56f0727f3b2acb184
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369830"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-postgresql"></a>Использование R2DBC Spring Data с Базой данных Azure для PostgreSQL

В этом разделе показано, как создать пример приложения для хранения информации в [Базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) и ее извлечения с помощью [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc), используя реализацию R2DBC для PostgreSQL из [репозитория GitHub r2dbc-postgresql](https://github.com/r2dbc/r2dbc-postgresql).

[R2DBC](https://r2dbc.io/) приносит реактивные API к традиционным реляционным базам данных. Вы можете применять это решение со Spring WebFlux для создания полностью реактивных приложений Spring Boot, которые используют неблокирующие API. Оно обеспечивает улучшенную масштабируемость по сравнению с классическим подходом "один поток на подключение".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="prepare-the-working-environment"></a>Подготовка среды выполнения

Сначала настройте некоторые переменные среды с помощью следующих команд:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=spring
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Замените заполнители следующими значениями, которые используются в этой статье:

- `<YOUR_DATABASE_NAME>`: Имя сервера PostgreSQL. Оно должно быть уникальным в Azure.
- `<YOUR_AZURE_REGION>`: регион Azure, который вы будете использовать. Вы можете использовать `eastus` по умолчанию, но мы рекомендуем настроить регион, расположенный близко к месту проживания. Полный список доступных регионов можно получить, введя `az account list-locations`.
- `<YOUR_POSTGRESQL_PASSWORD>`: Пароль для сервера базы данных PostgreSQL. Такой пароль должен содержать не менее восьми символов и включать знаки всех следующих типов: прописные латинские буквы, строчные латинские буквы, цифры (0–9) и небуквенно-цифровые знаки (!, $, #, % и т. д.).
- `<YOUR_LOCAL_IP_ADDRESS>`: IP-адрес локального компьютера, с которого будет запускаться приложение Spring Boot. Одним из удобных способов его поиска является ввод в обозревателе адреса [whatismyip.akamai.com](http://whatismyip.akamai.com/).

Затем создайте группу ресурсов:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Чтобы отобразить данные JSON и сделать их более удобными для чтения, мы используем служебную программу `jq`, которая по умолчанию устанавливается в [Azure Cloud Shell](https://shell.azure.com/).
> Если вам не нравится эта служебная программа, вы можете безопасно удалить `| jq` часть всех команд, которые мы будем использовать.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Создание экземпляра Базы данных Azure для PostgreSQL

Прежде всего мы создадим управляемый сервер PostgreSQL.

> [!NOTE]
> См. сведения о [создании сервера Базы данных Azure для PostgreSQL с помощью портала Azure](/azure/postgresql/quickstart-create-server-database-portal).

Выполните следующий скрипт в [Azure Cloud Shell](https://shell.azure.com/):

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    | jq
```

Эта команда создает небольшой сервер PostgreSQL.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>Настройка правила брандмауэра для сервера PostgreSQL

Экземпляры Базы данных Azure для PostgreSQL по умолчанию защищены. В них включен брандмауэр, который блокирует все входящие подключения. Чтобы вы могли использовать нашу базу данных, добавьте правило брандмауэра, которое разрешит локальному IP-адресу обращаться к серверу базы данных.

Так как вы настроили локальный IP-адрес ранее, вы можете открыть брандмауэр сервера, выполнив следующую команду:

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-postgresql-database"></a>Настройка базы данных PostgreSQL

Созданный вами ранее сервер PostgreSQL пуст. На нем отсутствует база данных, которую можно использовать с приложением Spring Boot. Создайте новую базу данных с именем `demo`:

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение, введя в командной строке следующую команду:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RC1 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-postgresql-driver-implementation"></a>Добавление реализации реактивного драйвера PostgreSQL

Откройте файл *pom.xml* созданного проекта, чтобы добавить реактивный драйвер PostgreSQL из [репозитория GitHub r2dbc-postgresql](https://github.com/r2dbc/r2dbc-postgresql).

После зависимости `spring-boot-starter-webflux` добавьте следующий фрагмент кода:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Настройка Spring Boot для использования Базы данных Azure для PostgreSQL

Откройте файл *src/main/resources/application.properties* и добавьте следующее:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:postgres://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_POSTGRESQL_PASSWORD
spring.r2dbc.properties.sslMode=REQUIRE
```

> [!WARNING]
> Из соображений безопасности для Базы данных Azure для PostgreSQL требуется использовать SSL-соединения. Поэтому вам нужно добавить свойство конфигурации `spring.r2dbc.properties.sslMode=REQUIRE`. В противном случае драйвер R2DBC PostgreSQL будет использовать небезопасное подключение, что приведет к сбою.

- Замените две переменные `$AZ_DATABASE_NAME` значением, которое вы настроили ранее.
- Замените переменную `$AZ_POSTGRESQL_PASSWORD` значением, которое вы настроили ранее.

> [!NOTE]
> Для лучшей производительности в качестве значения свойства `spring.r2dbc.url` указан пул подключений, как описано в репозитории [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Теперь вы можете запустить приложение с помощью предоставленной оболочки Maven:

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

Остановите приложение и запустите его снова. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

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

См. сведения об использовании Java в Azure в руководствах по использованию [Azure для разработчиков Java](/azure/developer/java/) и [Azure DevOps и Java](/azure/devops/).
