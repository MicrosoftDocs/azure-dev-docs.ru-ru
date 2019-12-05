---
title: Управление эластичными пулами баз данных SQL с помощью Java | Документация Майкрософт
description: Пример кода для создания и настройки баз данных SQL Azure с помощью пакета Azure SDK для Java
author: rloutlaw
ms.assetid: 9b461de8-46bc-4650-8e9e-59531f4e2a53
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 9a30217ccd336b0fa656910c9015615a95dc193a
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812331"
---
# <a name="manage-azure-sql-databases-in-elastic-pools-from-your-java-applications"></a>Управление базами данных SQL Azure в эластичных пулах с помощью приложений Java

[Этот пример](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool) создает сервер базы данных SQL с [эластичным пулом](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) для управления ресурсами нескольких баз данных в одном плане и их масштабирования.

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool
cd sql-database-java-manage-sql-dbs-in-elastic-pool
mvn clean compile exec:java
```

[Просмотрите полный пример кода на GitHub](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-sql-database-server-with-an-elastic-pool"></a>Создание сервера Базы данных SQL с эластичным пулом

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .withAdministratorLogin(administratorLogin)
                    .withAdministratorPassword(administratorPassword)
                    // Use ElasticPoolEditions.STANDARD as the edition
                    // and create two databases
                    .withNewElasticPool(elasticPoolName, ElasticPoolEditions.STANDARD, 
                        database1Name, database2Name)
                    .create();
```

Значения из текущего выпуска см. в [справочнике класса ElasticPoolEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._elastic_pool_editions). Чтобы сравнить характеристики ресурсов выпуска, см. [документацию по эластичному пулу баз данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool). 

## <a name="change-database-transaction-unit-dtu-settings-in-an-elastic-pool"></a>Изменение параметров единиц передачи данных (DTU) базы данных в эластичном пуле

```java
// Set an pool of 200 eDTUs to share between the databases
// and ensure that a database always has 10 DTUs available but never uses more than 50
elasticPool = elasticPool.update()
                    .withDtu(200)
                    .withDatabaseDtuMin(10)
                    .withDatabaseDtuMax(50)
                    .apply();
```

Дополнительные сведения о выделении ресурсов для баз данных SQL см. в [документации по DTU и eDTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu).

## <a name="create-a-new-database-and-add-it-to-an-elastic-pool"></a>Создание новой базы данных и ее добавление в эластичный пул

```java
// Add a newly created database to the elastic pool
SqlDatabase anotherDatabase = sqlServer.databases().define(anotherDatabaseName).create();
elasticPool.update().withExistingDatabase(anotherDatabase).apply();            
```

В первой инструкции API создает `anotherDatabase` на [уровне S0](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers). После перемещения `anotherDatabase` в эластичный пул базе данных присваиваются ресурсы на основе параметров пула.

## <a name="remove-a-database-from-an-elastic-pool"></a>Удаление базы данных из эластичного пула
```java
// Assign an edition since the database resources are no longer managed in the pool 
anotherDatabase = anotherDatabase.update()
                     .withoutElasticPool()
                     .withEdition(DatabaseEditions.STANDARD)
                     .apply();
```

Значения, которые нужно передать в `withEdition()`, см. в [справочнике по классу DatabaseEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._database_editions).

## <a name="list-current-database-activities-in-an-elastic-pool"></a>Получение списка текущих действий базы данных в эластичном пуле
```java
// get a list of in-flight elastic operations on databases in the pool and log them 
for (ElasticPoolDatabaseActivity databaseActivity : elasticPool.listDatabaseActivities()) {
    System.out.println("Database " + databaseActivity.databaseName() 
        + " performing operation " + databaseActivity.operation() + 
        " with objective " + databaseActivity.requestedServiceObjective());
}
```

Действия базы данных включают перемещение базы данных в эластичный пул или из него и обновление баз данных в эластичном пуле.


## <a name="list-databases-in-an-elastic-pool"></a>Получение списка баз данных в пуле эластичных БД
```java
// Log a list of databases in the elastic pool 
for (SqlDatabase databaseInServer : elasticPool.listDatabases()) {
    System.out.println(databaseInServer.name());
}
```

Инструкции по отправке более подробных запросов к базам данных см. в методах, приведенных в [этой статье](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._sql_database).

## <a name="delete-an-elastic-pool"></a>Удаление эластичного пула
```java
sqlServer.elasticPools().delete(elasticPoolName);
```

Прежде чем удалять эластичный пул, убедитесь, что он пуст.

## <a name="sample-code-summary"></a>Сводка примера кода

Пример создает сервер SQL с двумя базами данных, управляемыми в одном эластичном пуле. Ограничения ресурсов для эластичного пула обновляются, а затем в пул добавляются другие базы данных. Затем код считывает конфигурацию и состояние эластичного пула. 

Перед завершением пример удаляет все созданные им ресурсы.

| Класс, используемый в примере | Примечания |
|-------|-------|
| [SqlServer](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._sql_server) | Сервер базы данных SQL в Azure, созданный с помощью текучей цепочки `azure.sqlServers().define()...create()`. Указывает методы для создания эластичных пулов и баз данных и работы с ними. 
| [SqlDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._sql_database) | Объект на стороне клиента, представляющий базу данных SQL. Создан с помощью команды `sqlServer().define()...create()`. 
| [DatabaseEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._database_editions) | Постоянные статические поля, используемые для настройки ресурсов базы данных при создании базы данных за пределами эластичного пула или при перемещении базы данных за пределы эластичного пула.  
| [SqlElasticPool](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._sql_elastic_pool) | Создан из раздела `withNewElasticPool()` текучей цепочки, которая создала сервер SQL Server в Azure. Указывает методы для задания ограничений ресурсов для баз данных, запущенных в эластичном пуле, и для самого пула. 
| [ElasticPoolEditions](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._elastic_pool_editions) | Класс постоянных полей, определяющий ресурсы, доступные для эластичного пула. Сведения об уровне см в [документации по эластичному пулу базы данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool). 
| [ElasticPoolDatabaseActivity](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._elastic_pool_database_activity) | Получен из `SqlElasticPool.listDatabaseActivities()`. Каждый объект этого типа представляет действие, примененное к базе данных в эластичном пуле.
| [ElasticPoolActivity](https://docs.microsoft.com/java/api/com.microsoft.azure.management.sql._elastic_pool_activity) | Извлечен в виде списка из `SqlElasticPool.listActivities()`. Каждый объект в списке представляет действие, примененное к эластичному пулу (а не к базам данных в эластичном пуле).

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [next-steps](includes/java-next-steps.md)]