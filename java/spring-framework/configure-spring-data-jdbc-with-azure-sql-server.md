---
title: Как использовать JDBC Spring Data с Базой данных SQL Azure
description: Узнайте, как использовать JDBC Spring Data с базой данных SQL Azure.
services: sql-database
documentationcenter: java
ms.date: 12/19/2018
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 76fe515c6f315a340d5c306edfef31cdf736ceed
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811987"
---
# <a name="how-to-use-spring-data-jdbc-with-azure-sql-database"></a>Как использовать JDBC Spring Data с Базой данных SQL Azure

## <a name="overview"></a>Обзор

В этой статье показано создание примера приложения, использующего [Spring Data] для хранения и извлечения информации в [базу данных SQL Azure](https://azure.microsoft.com/services/sql-database/) с помощью [Java Database Connectivity (JDBC)](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* [Curl](https://curl.haxx.se/) или подобная служебная HTTP-программа, с помощью которой можно протестировать функциональные возможности.
* Клиент [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-sql-satabase"></a>Создание Базы данных Azure SQL

### <a name="create-a-sql-database-server-using-the-azure-portal"></a>Создание сервера базы данных SQL с помощью портала Azure

> [!NOTE]
> 
> Дополнительные сведения см. в статье о [создании базы данных Azure SQL с помощью портала Azure](/azure/sql-database/sql-database-get-started-portal).

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните элемент **+Create a resource** (+Создать ресурс), **Базы данных**, а затем выберите **База данных SQL**.

   ![Создание базы данных SQL][SQL01]

1. Укажите следующие сведения:

   - **Имя базы данных**. Выберите уникальное имя Базы данных SQL. Позже оно будет использоваться при создании сервера SQL.
   - **Подписка**: Укажите подписку Azure, которую нужно использовать.
   - **Группа ресурсов.** Укажите, следует ли создать группу ресурсов, или выберите имеющуюся группу ресурсов.
   - **Выберите источник**. В рамках данного руководства выберите `Blank database`, чтобы создать базу данных.

   ![Укажите свойства Базы данных SQL][SQL02]
   
1. Щелкните **Сервер**, после этого выберите **Создать сервер**, а затем укажите следующие сведения:

   - **Имя сервера**. Для сервера SQL выберите уникальное имя. Это имя будет использоваться для создания полного доменного имени, например *wingtiptoyssql.database.windows.net*.
   - **Учетные данные администратора сервера для входа**. Укажите имя администратора базы данных.
   - **Пароль** и **Подтверждение пароля**. Укажите пароль администратора базы данных.
   - **Расположение.** Укажите ближайший географический регион для базы данных.

   ![Укажите SQL сервер][SQL03]

1. После ввода всех этих данных нажмите кнопку **Выбрать**.

1. В рамках этого руководства укажите самую низкую **ценовую категорию**, а затем щелкните **Создать**.

   ![Создание Базы данных SQL][SQL04]

### <a name="configure-a-firewall-rule-for-your-sql-server-using-the-azure-portal"></a>Настройка правила брандмауэра для SQL-сервера с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданный SQL сервер.

   ![Выбор SQL сервера][SQL05]

1. В разделе **Обзор** щелкните **Показать параметры брандмауэра**

   ![Показать параметры брандмауэра][SQL06]

1. В разделе **Брандмауэры и виртуальные сети** создайте правило, указав для него уникальное имя, а затем введите диапазон IP-адресов, которым потребуется доступ к базе данных, и нажмите кнопку **Сохранить**.

   ![Настройка параметров брандмауэра][SQL07]

### <a name="retrieve-the-connection-string-for-your-sql-server-using-the-azure-portal"></a>Получение строки подключения для SQL-сервера на портале Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданную Базу данных SQL.

   ![Выбор Базы данных SQL][SQL08]

1. Нажмите кнопку **Строки подключения**, затем щелкните**JDBC** и скопируйте значение в текстовое поле JDBC.

   ![Получение строки подключения JDBC][SQL09]

## <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте командную строку и клонируйте пример проекта с помощью команды Git, как в следующем примере:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jdbc-on-azure.git
   ```

1. Найдите файл *application.properties* в каталоге *resources* примера приложения или создайте его, если он еще не существует.

1. Откройте файл *application.properties* в текстовом редакторе, добавьте или настройте указанные ниже строки в файл и замените примеры значений на соответствующие полученные ранее значения:

   ```yaml
   spring.datasource.url=jdbc:sqlserver://wingtiptoyssql.database.windows.net:1433;database=wingtiptoys;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
   spring.datasource.username=wingtiptoysuser@wingtiptoyssql
   spring.datasource.password=********
    ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `spring.datasource.url` | Указывается измененная версия строки SQL JDBC, описанная ранее в этой статье. |
   | `spring.datasource.username` | Указывается администратор SQL, описанный ранее в этой статье вместе с сокращенным именем сервера. |
   | `spring.datasource.password` | Указывается пароль администратора SQL, описанный ранее в этой статье. |

1. Сохраните и закройте файл *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Упаковывание и тестирование примера приложения 

1. Создайте пример приложения с помощью Maven, например:

   ```shell
   mvn clean package -P sql
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

С помощью этого руководства вы создали пример приложения Java, использующий Spring Data для хранения и извлечения информации в базу данных SQL Azure с помощью JDBC.

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

[SQL01]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-01.png
[SQL02]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-02.png
[SQL03]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-03.png
[SQL04]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-04.png
[SQL05]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-05.png
[SQL06]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-06.png
[SQL07]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-07.png
[SQL08]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-08.png
[SQL09]: media/configure-spring-data-jdbc-with-azure-sql-server/create-azure-sql-09.png
