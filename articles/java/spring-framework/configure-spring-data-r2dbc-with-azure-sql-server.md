---
title: Использование R2DBC Spring Data с Базой данных SQL Azure
description: Узнайте, как использовать R2DBC Spring Data с Базой данных SQL Azure.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 057f261de707adac2ab3ef9ac52834a9848362b6
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369991"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Использование R2DBC Spring Data с Базой данных SQL Azure

В этом разделе показано, как создать пример приложения для хранения информации в [Базе данных SQL](https://docs.microsoft.com/azure/sql-database/) и ее извлечения с помощью [R2DBC Spring Data](https://spring.io/projects/spring-data-r2dbc), используя реализацию R2DBC для Microsoft SQL Server из [репозитория GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

[R2DBC](https://r2dbc.io/) приносит реактивные API к традиционным реляционным базам данных. Вы можете применять это решение со Spring WebFlux для создания полностью реактивных приложений Spring Boot, которые используют неблокирующие API. Оно обеспечивает улучшенную масштабируемость по сравнению с классическим подходом "один поток на подключение".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="prepare-the-working-environment"></a>Подготовка среды выполнения

Сначала настройте некоторые переменные среды с помощью следующих команд:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Замените заполнители следующими значениями, которые используются в этой статье:

- `<YOUR_DATABASE_NAME>`: Имя сервера Базы данных SQL Azure. Оно должно быть уникальным в Azure.
- `<YOUR_AZURE_REGION>`: регион Azure, который вы будете использовать. Вы можете использовать `eastus` по умолчанию, но мы рекомендуем настроить регион, расположенный близко к месту проживания. Полный список доступных регионов можно получить, введя `az account list-locations`.
- `<AZ_SQL_SERVER_PASSWORD>`: Пароль для сервера Базы данных SQL Azure. Такой пароль должен содержать не менее восьми символов и включать знаки всех следующих типов: прописные латинские буквы, строчные латинские буквы, цифры (0–9) и небуквенно-цифровые знаки (!, $, #, % и т. д.).
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

## <a name="create-an-azure-sql-database-instance"></a>Создание экземпляра Базы данных SQL Azure

Для начала мы создадим управляемый сервер Базы данных SQL Azure.

> [!NOTE]
> См. сведения о создании сервера Базы данных SQL Azure в руководстве по [ созданию отдельной базы данных в Базе данных SQL Azure](/azure/sql-database/sql-database-single-database-get-started).

Выполните следующий скрипт в [Azure Cloud Shell](https://shell.azure.com/):

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

Эта команда создает сервер Базы данных SQL Azure.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Настройка правила брандмауэра для сервера Базы данных SQL Azure

Экземпляры Базы данных SQL Azure по умолчанию защищены. В них включен брандмауэр, который блокирует все входящие подключения. Чтобы вы могли использовать нашу базу данных, добавьте правило брандмауэра, которое разрешит локальному IP-адресу обращаться к серверу базы данных.

Так как вы настроили локальный IP-адрес ранее, вы можете открыть брандмауэр сервера, выполнив следующую команду:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Настройка базы данных SQL Azure

Сервер Базы данных SQL Azure, который вы создали ранее, сейчас пустой. На нем отсутствует база данных, которую можно использовать с приложением Spring Boot. Создайте новую базу данных с именем `demo`:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте приложение, введя в командной строке следующую команду:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RC1 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Добавление реализации реактивного драйвера Базы данных SQL Azure

Откройте файл *pom.xml* в созданном проекте, чтобы добавить реактивный драйвер Базы данных SQL Azure из [репозитория GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

После зависимости `spring-boot-starter-webflux` добавьте следующий фрагмент кода:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Настройка Spring Boot для использования Базы данных SQL Azure

Откройте файл *src/main/resources/application.properties* и добавьте следующее:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

- Замените две переменные `$AZ_DATABASE_NAME` значением, которое вы настроили ранее.
- Замените переменную `$AZ_SQL_SERVER_PASSWORD` значением, которое вы настроили ранее.

> [!NOTE]
> Для лучшей производительности в качестве значения свойства `spring.r2dbc.url` указан пул подключений, как описано в репозитории [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Теперь вы можете запустить приложение с помощью предоставленной оболочки Maven:

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

Остановите приложение и запустите его снова. Теперь приложение будет использовать созданную ранее базу данных `demo` и создаст в ней таблицу `todo`.

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
