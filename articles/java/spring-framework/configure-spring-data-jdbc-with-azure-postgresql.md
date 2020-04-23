---
title: Как использовать JDBC Spring Data с базой данных Azure PostgreSQL
description: Узнайте, как использовать JDBC Spring Data с базой данных Azure PostgreSQL.
services: postgresql
documentationcenter: java
ms.date: 12/26/2019
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: f259ac4b169f3533c0f5eabd179dd79cc45b708b
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81669630"
---
# <a name="how-to-use-spring-data-jdbc-with-azure-postgresql"></a>Как использовать JDBC Spring Data с базой данных Azure PostgreSQL

В этой статье показано, как создать пример приложения, использующего [Spring Data] для хранения и извлечения информации в базу данных Azure [PostgreSQL](https://www.postgresql.org/) с помощью [Java Database Connectivity (JDBC)](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* [Curl](https://curl.haxx.se/) или подобная служебная HTTP-программа, с помощью которой можно протестировать функциональные возможности.
* Служебная программа командной строки [psql](https://www.postgresql.org/docs/current/app-psql.html).
* Клиент [Git](https://git-scm.com/downloads).

## <a name="create-a-postgresql-database-for-azure"></a>Создание базы данных PostgreSQL в Azure

### <a name="create-a-postgresql-database-server-using-the-azure-portal"></a>Создание сервера базы данных PostgreSQL с помощью портала Azure

> [!NOTE]
> 
> Дополнительные сведения см. в статье о [создании базы данных Azure для сервера PostgreSQL с помощью портала Azure](/azure/postgresql/quickstart-create-server-database-portal).

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните элемент **+Create a resource** (+Создать ресурс), **Базы данных**, а затем выберите **База данных Azure для PostgreSQL**.

   ![Создание базы данных PostgreSQL][POSTGRESQL01]

1. Введите следующие сведения:

   - **Группа ресурсов.** Укажите, следует ли создать группу ресурсов, или выберите имеющуюся группу ресурсов.
   - **Подписка**: Укажите подписку Azure, которую нужно использовать.
   - **Имя сервера**: Для сервера PostgreSQL выберите уникальное имя. Это имя будет использоваться для создания полного доменного имени, например *wingtiptoyspostgresql.postgres.database.azure.com*.
   - **Выберите источник**. В рамках данного руководства выберите `None`, чтобы создать базу данных.
   - **Имя для входа администратора сервера**. Укажите имя администратора базы данных.
   - **Пароль** и **Подтверждение пароля**. Укажите пароль администратора базы данных.
   - **Расположение.** Укажите ближайший географический регион для базы данных.
   - **Версия.** Укажите самую последнюю версию базы данных.

   ![Создание свойств базы данных PostgreSQL][POSTGRESQL02]

1. После ввода всех этих данных нажмите кнопку **Проверка и создание**.

1. Проверьте спецификации и щелкните **Создать**.

### <a name="configure-a-firewall-rule-for-your-postgresql-database-server-using-the-azure-portal"></a>Настройка правила брандмауэра для сервера базы данных PostgreSQL с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданную базу данных PostgreSQL.

1. Щелкните **Безопасность подключения**, после этого создайте правило на вкладке **Правила брандмауэра**, указав для него уникальное имя, а затем введите диапазон IP-адресов, которым потребуется доступ к базе данных, и нажмите кнопку **Сохранить**. (В этом упражнении мы используем IP-адрес компьютера разработки, который является клиентом.  Его можно использовать в качестве **начального** и **конечного** IP-адресов.

   ![Настройка безопасности подключения][POSTGRESQL03]

### <a name="retrieve-the-connection-string-for-your-postgresql-server-using-the-azure-portal"></a>Получение строки подключения для сервера PostgreSQL с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданную базу данных PostgreSQL.

1. Нажмите кнопку **Строки подключения** и скопируйте значение в текстовое поле **JDBC**.

   ![Получение строки подключения JDBC][POSTGRESQL05]

### <a name="create-postgresql-database-using-the-psql-command-line-utility"></a>Создание базы данных PostgreSQL с помощью служебной программы командной строки `psql`

1. Откройте командную оболочку и подключитесь к серверу PostgreSQL, введя команду `psql`, как в следующем примере:

   ```shell
   psql --host=wingtiptoyspostgresql.postgres.database.azure.com --port=5432 --username=wingtiptoysuser@wingtiptoyspostgresql --dbname=postgres
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `host` | Указывается полное доменное имя сервера PostgreSQL, описанное ранее в этой статье. |
   | `host` | Указывается порт сервера PostgreSQL, то есть по умолчанию — `5432`. |
   | `username` | Указывается администратор PostgreSQL и сокращенное имя сервера, описанные ранее в этой статье. |
   | `dbname` | Указывается, хотите ли вы сейчас использовать по умолчанию базу данных `postgres`. |

   Сервер PostgreSQL должен отобразить экран, как в следующем примере:

   ```shell
   psql (9.3.24, server 10.5)
   SSL connection (cipher: ECDHE-RSA-AES256-SHA384, bits: 256)
   Type "help" for help.
   
   postgres=>
   ```
   > Примечание. Если вы получили сообщение о том, что сервер не распознает этот IP-адрес, в сообщении об ошибке будет отображаться IP-адрес, который используется клиентом.  Назначьте его, как описано выше. *Настройка правила брандмауэра для сервера с помощью портала Azure*

1. Создайте базу данных с именем *mypgsqldb*, введя команду `psql`, как в следующем примере:

   ```SQL
   CREATE DATABASE mypgsqldb;
   ```

   Сервер PostgreSQL должен отобразить экран, как в следующем примере:

   ```shell
   CREATE DATABASE
   ```

1. НЕОБЯЗАТЕЛЬНО. Убедитесь, что база данных создана, введя `\l` в `psql`. Сервер PostgreSQL должен вернуть ответ примерно такого содержания, как в следующем примере:

   ```shell
                   List of databases
          Name        |      Owner      | Encoding
   -------------------+-----------------+----------
    azure_maintenance | azure_superuser | UTF8
    azure_sys         | azure_superuser | UTF8
    mypgsqldb         | wingtiptoysuser | UTF8
    postgres          | azure_superuser | UTF8
    template0         | azure_superuser | UTF8
    template1         | azure_superuser | UTF8
   (6 rows)
   ```

1. Введите `\q` для выхода из служебной программы `psql`.

## <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте командную строку и клонируйте пример проекта с помощью команды Git, как в следующем примере:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jdbc-on-azure.git
   ```

1. Найдите файл *application.properties* в каталоге *resources* примера приложения или создайте его, если он еще не существует.

1. Откройте файл *application.properties* в текстовом редакторе, добавьте или настройте указанные ниже строки в файл и замените примеры значений на соответствующие полученные ранее значения:

   ```yaml
   spring.datasource.url=jdbc:postgresql://wingtiptoyspostgresql.postgres.database.azure.com:5432/mypgsqldb?ssl=true&sslmode=prefer
   spring.datasource.username=wingtiptoysuser@wingtiptoyspostgresql
   spring.datasource.password=********
    ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `spring.datasource.url` | Указываются строки PostgreSQL JDBC, описанные ранее в этой статье. |
   | `spring.datasource.username` | Указывается администратор PostgreSQL, описанный ранее в этой статье, вместе с сокращенным именем сервера. |
   | `spring.datasource.password` | Указывается пароль администратора PostgreSQL, описанный ранее в этой статье. |

1. Сохраните и закройте файл *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Упаковывание и тестирование примера приложения 

1. Создайте пример приложения с помощью Maven, например:

   ```shell
   mvn clean package -P postgresql
   ```

1. Запустите пример приложения, например:

   ```shell
   java -jar target/spring-data-jdbc-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Из командной строки создайте записи с помощью `curl`, как в следующем примере:

   ```shell
   curl -s -d '{"name":"dog","species":"canine"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d '{"name":"cat","species":"feline"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Приложение должно возвращать значения следующим образом:

   ```shell
   Added Pet(id=1, name=dog, species=canine).

   Added Pet(id=2, name=cat, species=feline).
   ```

1. Получите все имеющиеся записи из командной строки с помощью `curl`, как в следующем примере:

   ```shell
   curl -s http://localhost:8080/pets
   ```
    
   Приложение должно возвращать значения следующим образом:

   ```json
   [{"id":1,"name":"dog","species":"canine"},{"id":2,"name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Сводка

С помощью этого руководства вы создали пример приложения Java, использующий Spring Data для хранения и извлечения информации в базу данных Azure PostgreSQL с помощью JDBC.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Azure для разработчиков Java]: /azure/developer/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[POSTGRESQL01]: media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png
[POSTGRESQL02]: media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png
[POSTGRESQL03]: media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-03.png
[POSTGRESQL04]: media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-04.png
[POSTGRESQL05]: media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-05.png