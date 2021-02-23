---
title: Использование JavaScript в Azure MySQL
description: Для создания или перемещения базы данных MySQL в Azure необходим ресурс MySQL.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: caab884c0a943f00ccc1701a7d364bb0825bce78
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005024"
---
# <a name="develop-a-javascript-application-with-mysql-on-azure"></a>Разработка приложения JavaScript с MySQL в Azure

Для создания, перемещения или использования базы данных MySQL в Azure необходим ресурс **Базы данных Azure для MySQL**. Узнайте, как создать ресурс и использовать свою базу данных.

## <a name="create-an-azure-database-for-mysql-resource"></a>Создание ресурса Базы данных Azure для MySQL 

Ресурс можно создать с помощью таких средств:

* Azure CLI
* [Портал Azure](https://ms.portal.azure.com/#create/Microsoft.MySQLServer)
* [@azure/arm-mysql](https://www.npmjs.com/package/@azure/arm-mysql)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mysql-db.md)]

## <a name="view-and-use-your-mysql-on-azure"></a>Использование базы данных MySQL и просмотр данных о ней в Azure
При разработке базы данных MySQL с помощью JavaScript используйте одно из следующих средств:

* CLI _mysql_ в [Azure Cloud Shell](https://shell.azure.com/).
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* [Расширение](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql) Visual Studio Code.

## <a name="use-sdk-packages-to-develop-your-mysql-on-azure"></a>Использование пакетов SDK для разработки MySQL в Azure

Azure MySQL использует уже доступные пакеты npm, например:

* [MySQL](https://www.npmjs.com/package/MySQL)
* [Promise-mysql.](https://www.npmjs.com/package/promise-mysql)

## <a name="use-promise-mysql-sdk-to-connect-to-mysql-on-azure"></a>Использование пакета SDK Promise-mysql для подключения к MySQL в Azure

Чтобы подключить и использовать MySQL в Azure с помощью JavaScript, выполните следующую процедуру:

1. Убедитесь, что установлены Node.js и npm.
1. Создайте проект Node.js в новой папке:

    ```bash
    mkdir MySQLDemo && \
        cd MySQLDemo && \
        npm init -y && \
        npm install promise-mysql && \
        touch index.js && \
        code .
    ```

    Эта команда выполняет следующие действия:
    * создает папку проекта с именем `MySQLDemo`;
    * выполняет переход в терминале Bash к этой папке;
    * инициализирует проект, что приводит к созданию файла `package.json`;
    * устанавливает пакет npm Promise-mysql для использования async/await;
    * создает файл скрипта `index.js`;
    * открывает проект в Visual Studio Code.

1. Скопируйте следующий код JavaScript в файл `index.js`:

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install promise-mysql

    // get MySQL SDK
    const MySQL = require('promise-mysql');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await MySQL.createConnection(config);

      // show databases on server
      const databases = await connection.query('SHOW DATABASES;');
      console.log(databases);

      // show tables in the mysql database
      const tables = await connection.query('SHOW TABLES FROM mysql;');
      console.log(tables);

      // show users configured for the server
      const rows = await connection.query('select User from mysql.user;');
      console.log(rows);

      // close connection
      connection.end();
    };

    const config = {
      host: 'YOUR-RESOURCE_NAME.mysql.database.azure.com',
      user: 'YOUR-ADMIN-NAME@YOUR-RESOURCE_NAME',
      password: 'YOUR-ADMIN-PASSWORD',
      port: 3306,
    };

    query(config)
      .then(() => console.log('done'))
      .catch((err) => console.log(err));
    ```

1. В скрипте для объекта конфигурации подключения `config` укажите для узла, пользователя и пароля свои значения. 

1. Выполните скрипт.

    ```bash
    [
      RowDataPacket { Database: 'information_schema' },
      RowDataPacket { Database: 'defaultdb' },
      RowDataPacket { Database: 'dbproducts' },
      RowDataPacket { Database: 'mysql' },
      RowDataPacket { Database: 'performance_schema' },
      RowDataPacket { Database: 'sys' }
    ]
    [
      RowDataPacket { Tables_in_mysql: '__az_action_history__' },
      RowDataPacket { Tables_in_mysql: '__az_changed_static_configs__' },
      RowDataPacket { Tables_in_mysql: '__az_replica_information__' },
      RowDataPacket { Tables_in_mysql: '__az_replication_current_state__' },
      RowDataPacket { Tables_in_mysql: '__firewall_rules__' },
      RowDataPacket { Tables_in_mysql: '__querystore_event_wait__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_metrics__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_text__' },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_errors__'
      },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_status__'
      },
      RowDataPacket { Tables_in_mysql: '__recommendation__' },
      RowDataPacket { Tables_in_mysql: '__recommendation_session__' },
      RowDataPacket { Tables_in_mysql: '__script_version__' },
      RowDataPacket { Tables_in_mysql: 'columns_priv' },
      RowDataPacket { Tables_in_mysql: 'db' },
      RowDataPacket { Tables_in_mysql: 'engine_cost' },
      RowDataPacket { Tables_in_mysql: 'event' },
      RowDataPacket { Tables_in_mysql: 'func' },
      RowDataPacket { Tables_in_mysql: 'general_log' },
      RowDataPacket { Tables_in_mysql: 'gtid_executed' },
      RowDataPacket { Tables_in_mysql: 'help_category' },
      RowDataPacket { Tables_in_mysql: 'help_keyword' },
      RowDataPacket { Tables_in_mysql: 'help_relation' },
      RowDataPacket { Tables_in_mysql: 'help_topic' },
      RowDataPacket { Tables_in_mysql: 'innodb_index_stats' },
      RowDataPacket { Tables_in_mysql: 'innodb_table_stats' },
      RowDataPacket { Tables_in_mysql: 'ndb_binlog_index' },
      RowDataPacket { Tables_in_mysql: 'plugin' },
      RowDataPacket { Tables_in_mysql: 'proc' },
      RowDataPacket { Tables_in_mysql: 'procs_priv' },
      RowDataPacket { Tables_in_mysql: 'proxies_priv' },
      RowDataPacket { Tables_in_mysql: 'query_store' },
      RowDataPacket { Tables_in_mysql: 'query_store_wait_stats' },
      RowDataPacket { Tables_in_mysql: 'recommendation' },
      RowDataPacket { Tables_in_mysql: 'server_cost' },
      RowDataPacket { Tables_in_mysql: 'servers' },
      RowDataPacket { Tables_in_mysql: 'slave_master_info' },
      RowDataPacket { Tables_in_mysql: 'slave_relay_log_info' },
      RowDataPacket { Tables_in_mysql: 'slave_worker_info' },
      RowDataPacket { Tables_in_mysql: 'slow_log' },
      RowDataPacket { Tables_in_mysql: 'tables_priv' },
      RowDataPacket { Tables_in_mysql: 'time_zone' },
      RowDataPacket { Tables_in_mysql: 'time_zone_leap_second' },
      RowDataPacket { Tables_in_mysql: 'time_zone_name' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition_type' },
      RowDataPacket { Tables_in_mysql: 'user' }
    ]
    [
      RowDataPacket { User: 'mySqlAdmin' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'mysql.session' },
      RowDataPacket { User: 'mysql.sys' }
    ]
    done
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание веб-приложения JavaScript](../deploy-web-app.md)
* [База данных Azure для MySQL](/azure/mysql/)
* [Миграция с использованием дампа и восстановления](/azure/mysql/concepts-migrate-dump-restore)
* [Миграция с помощью MySQL Workbench](/azure/mysql/concepts-migrate-import-export)