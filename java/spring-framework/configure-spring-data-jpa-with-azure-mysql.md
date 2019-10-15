---
title: Как использовать JPA Spring Data с Базой данных Azure для MySQL
description: Узнайте, как настроить JPA Spring Data и использовать этот API с Базой данных Azure для MySQL.
documentationcenter: java
author: bmitchell287
manager: douge
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: conceptual
ms.openlocfilehash: 842dc7785f8c7c84d6e9ba464c96d65db75dc9fd
ms.sourcegitcommit: 2610f3992cb6d21a3657032074acb981d130fdad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960773"
---
# <a name="how-to-use-spring-data-jpa-with-azure-database-for-mysql"></a>Как использовать JPA Spring Data с Базой данных Azure для MySQL

## <a name="overview"></a>Обзор

В этой статье объясняется, как создать пример приложения для хранения информации в [Базе данных Azure для MySQL](/azure/mysql/) с помощью [Spring Data] и извлечения информации из базы данных с помощью [Java Persistence API (JPA)](https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* [Curl](https://curl.haxx.se/) или подобная служебная HTTP-программа, с помощью которой можно протестировать функциональные возможности.
* Служебная программа командной строки [mysql](https://dev.mysql.com/downloads/).
* Клиент [Git](https://git-scm.com/downloads).

## <a name="create-a-azure-database-for-mysql-server"></a>Создание сервера Базы данных Azure для сервера MySQL

### <a name="create-a-server-using-the-azure-portal"></a>Создание сервера с помощью портала Azure

> [!NOTE]
> 
> Дополнительные сведения см. в статье о [создании базы данных Azure для сервера MySQL с помощью портала Azure](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

1. Перейдите на [портал Azure](https://portal.azure.com) и выполните вход.

1. Выберите **+Создать ресурс**, **Базы данных**, а затем щелкните **База данных Azure для MySQL**.

   ![Создание базы данных MySQL][MYSQL01]

1. Введите следующие сведения:

   - **Имя сервера**. Для сервера Базы данных Azure для MySQL выберите уникальное имя. Оно будет использоваться для создания полного доменного имени, например *wingtiptoysmysql.mysql.database.azure.com*.
   - **Подписка**: Укажите подписку Azure, которую нужно использовать.
   - **Группа ресурсов.** Укажите, следует ли создать группу ресурсов, или выберите имеющуюся группу ресурсов.
   - **Выберите источник**. В рамках данного руководства выберите `Blank`, чтобы создать базу данных.
   - **Учетные данные администратора сервера для входа**. Укажите имя администратора базы данных.
   - **Пароль** и **Подтверждение пароля**. Укажите пароль администратора базы данных.
   - **Расположение.** Укажите ближайший географический регион для базы данных.
   - **Версия.** Укажите самую последнюю версию базы данных.
   - **Ценовая категория**. В рамках этого руководства укажите самую низкую ценовую категорию.

   ![Создание свойств базы данных MySQL][MYSQL02]

1. После ввода всех этих данных нажмите кнопку **Создать**.

### <a name="configure-a-firewall-rule-for-your-server-using-the-azure-portal"></a>Настройка правила брандмауэра для сервера с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданную базу данных MySQL.

   ![Выбор базы данных MySQL][MYSQL03]

1. Щелкните **Безопасность подключения**, после этого создайте правило на вкладке **Правила брандмауэра**, указав для него уникальное имя, а затем введите диапазон IP-адресов, которым потребуется доступ к базе данных, и нажмите кнопку **Сохранить**.

   ![Настройка безопасности подключения][MYSQL04]

### <a name="retrieve-the-connection-string-for-your-server-using-the-azure-portal"></a>Получение строки подключения для сервера с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните **Все ресурсы**, а затем выберите только что созданный ресурс Базы данных Azure для MySQL.

   ![Выбор базы данных MySQL][MYSQL03]

1. Нажмите кнопку **Строки подключения** и скопируйте значение в текстовое поле **JDBC**.

   ![Получение строки подключения JDBC][MYSQL05]

### <a name="create-a-database-using-the-mysql-command-line-utility"></a>Создание базы данных с помощью служебной программы командной строки `mysql`

1. Откройте командную оболочку и подключитесь к серверу Базы данных Azure для MySQL. Для этого введите команду `mysql`, как в следующем примере:

   ```shell
   mysql --host wingtiptoysmysql.mysql.database.azure.com --user wingtiptoysuser@wingtiptoysmysql -p
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `host` | Указывается полное доменное имя сервера MySQL, описанное ранее в этой статье. |
   | `user` | Указываются администратор MySQL и сокращенное имя сервера, описанные ранее в этой статье. |
   | `p` | Указывается, что следует ожидать запрос пароля. |


   Сервер MySQL должен отобразить экран, как в следующем примере:

   ```shell
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64552
   Server version: 5.6.39.0 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```

1. Создайте базу данных с именем *mysqldb*, введя команду `mysql`, как в следующем примере:

   ```SQL
   CREATE DATABASE mysqldb;
   ```

   Сервер MySQL должен отобразить экран, как в следующем примере:

   ```shell
   Query OK, 1 row affected (0.30 sec)
   ```

1. НЕОБЯЗАТЕЛЬНО. Убедитесь, что база данных создана, введя команду `mysql`, как в следующем примере:

   ```SQL
   SHOW DATABASES;
   ```

   Сервер MySQL должен отобразить экран, как в следующем примере:

   ```shell
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | mysqldb            |
   | performance_schema |
   | sys                |
   +--------------------+
   ```

1. Введите `\q` для выхода из служебной программы `mysql`.

## <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте командную строку и клонируйте пример проекта с помощью команды Git, как в следующем примере:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jpa-on-azure.git
   ```

1. Найдите файл *application.properties* в каталоге *resources* примера приложения или создайте его, если он еще не существует.

1. Откройте файл *application.properties* в текстовом редакторе, добавьте или настройте указанные ниже строки в файл и замените примеры значений на соответствующие полученные ранее значения:

   ```yaml
   spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
   spring.datasource.url=jdbc:mysql://wingtiptoysmysql.mysql.database.azure.com:3306/mysqldb?useSSL=true&requireSSL=false
   spring.datasource.username=wingtiptoysuser@wingtiptoysmysql
   spring.datasource.password=********
    ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `spring.jpa.database-platform` | Указывается платформа баз данных JPA. |
   | `spring.datasource.url` | Указываются строки MySQL JDBC, описанные ранее в этой статье. |
   | `spring.datasource.username` | Указывается администратор MySQL, описанный ранее в этой статье, вместе с сокращенным именем сервера. |
   | `spring.datasource.password` | Указывается пароль администратора MySQL, описанный ранее в этой статье. |

1. Сохраните и закройте файл *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Упаковывание и тестирование примера приложения 

1. Создайте пример приложения с помощью Maven, например:

   ```shell
   mvn clean package -P mysql
   ```

1. Запустите пример приложения, например:

   ```shell
   java -jar target/spring-data-jpa-on-azure-0.1.0-SNAPSHOT.jar
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

В рамках этого руководства вы создали пример приложения Java для хранения информации в Базе данных Azure для MySQL с помощью Spring Data и извлечения информации из базы данных с помощью JPA.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Azure для разработчиков Java]: /azure/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[MYSQL01]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png
[MYSQL02]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png
[MYSQL03]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-03.png
[MYSQL04]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-04.png
[MYSQL05]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-05.png
