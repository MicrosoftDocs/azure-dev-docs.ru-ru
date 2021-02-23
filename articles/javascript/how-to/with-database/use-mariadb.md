---
title: Использование JavaScript в Azure MariaDB
description: Для создания или перемещения базы данных MariaDB в Azure необходим ресурс MariaDB.
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 320397584f78809e39e7eeaf7c1c6805755a82de
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004733"
---
# <a name="develop-a-javascript-application-with-mariadb-on-azure"></a>Разработка приложения JavaScript с MariaDB в Azure

Для создания, перемещения или использования базы данных MariaDB в Azure необходим ресурс **Базы данных Azure для MariaDB**. Узнайте, как создать ресурс и использовать свою базу данных.

## <a name="create-an-azure-database-for-mariadb-resource"></a>Создание Базы данных Azure для ресурса MariaDB 

Ресурс можно создать с помощью таких средств:

* Azure CLI
* [Портал Azure](https://ms.portal.azure.com/#create/Microsoft.MariaDBServer)
* [@azure/arm-mariadb](https://www.npmjs.com/package/@azure/arm-mariadb)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mariadb.md)]

## <a name="view-and-use-your-mariadb-on-azure"></a>Просмотр и использование MariaDB в Azure
При разработке базы данных MariaDB с помощью JavaScript используйте одно из следующих средств:

* CLI _mysql_ в [Azure Cloud Shell](https://shell.azure.com/)
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* [Расширение](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql) Visual Studio Code.

## <a name="use-sdk-packages-to-develop-your-mariadb-on-azure"></a>Использование пакетов SDK для разработки MariaDB в Azure

Azure MariaDB использует уже доступные пакеты npm, например:

* [mariadb](https://www.npmjs.com/package/mariadb)

## <a name="use-mariadb-sdk-to-connect-to-mariadb-on-azure"></a>Использование пакета SDK MariaDB для подключения к MariaDB в Azure

Чтобы подключить и использовать MariaDB в Azure с помощью JavaScript, выполните следующую процедуру.

1. Убедитесь, что установлены Node.js и npm.
1. Создайте проект Node.js в новой папке:

    ```bash
    mkdir mariaDbDemo && \
        cd mariaDbDemo && \
        npm init -y && \
        npm install mariadb && \
        touch index.js && \
        code .
    ```

    Эта команда:
    * создает папку проекта с именем `mariaDbDemo`;
    * выполняет переход в терминале Bash к этой папке;
    * инициализирует проект, что приводит к созданию файла `package.json`;
    * устанавливает пакет npm mariadb;
    * создает файл скрипта `index.js`;
    * открывает проект в Visual Studio Code.

1. Скопируйте следующий код JavaScript в файл `index.js`:

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install mariadb

    // get mariadb SDK
    const mariadb = require('mariadb');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await mariadb.createConnection(config);

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
      host: 'YOUR-RESOURCE_NAME.mariadb.database.azure.com',
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
      { Database: 'information_schema' },
      { Database: 'mysql' },
      { Database: 'performance_schema' },
      { Database: 'quickstartdb' },
      { Database: 'tutorial' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 256,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { Tables_in_mysql: '__az_action_history__' },
      { Tables_in_mysql: '__az_changed_static_configs__' },
      { Tables_in_mysql: '__az_replica_information__' },
      { Tables_in_mysql: '__az_replication_current_state__' },
      { Tables_in_mysql: '__az_slave_relay_log_info__' },
      { Tables_in_mysql: '__firewall_rules__' },
      { Tables_in_mysql: '__script_version__' },
      { Tables_in_mysql: 'column_stats' },
      { Tables_in_mysql: 'columns_priv' },
      { Tables_in_mysql: 'db' },
      { Tables_in_mysql: 'event' },
      { Tables_in_mysql: 'func' },
      { Tables_in_mysql: 'general_log' },
      { Tables_in_mysql: 'gtid_slave_pos' },
      { Tables_in_mysql: 'help_category' },
      { Tables_in_mysql: 'help_keyword' },
      { Tables_in_mysql: 'help_relation' },
      { Tables_in_mysql: 'help_topic' },
      { Tables_in_mysql: 'host' },
      { Tables_in_mysql: 'index_stats' },
      { Tables_in_mysql: 'innodb_index_stats' },
      { Tables_in_mysql: 'innodb_table_stats' },
      { Tables_in_mysql: 'plugin' },
      { Tables_in_mysql: 'proc' },
      { Tables_in_mysql: 'procs_priv' },
      { Tables_in_mysql: 'proxies_priv' },
      { Tables_in_mysql: 'roles_mapping' },
      { Tables_in_mysql: 'servers' },
      { Tables_in_mysql: 'slow_log' },
      { Tables_in_mysql: 'table_stats' },
      { Tables_in_mysql: 'tables_priv' },
      { Tables_in_mysql: 'time_zone' },
      { Tables_in_mysql: 'time_zone_leap_second' },
      { Tables_in_mysql: 'time_zone_name' },
      { Tables_in_mysql: 'time_zone_transition' },
      { Tables_in_mysql: 'time_zone_transition_type' },
      { Tables_in_mysql: 'transaction_registry' },
      { Tables_in_mysql: 'user' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 292,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { User: 'azureAdmin' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 320,
          columnType: 254,
          flags: 16515,
          scale: 0,
          type: 'STRING'
        }
      ]
    ]
    done
    ```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [развернуть веб-приложение JavaScript](../deploy-web-app.md)
* [База данных Azure для MariaDB](/azure/mariadb/)
* [Руководство по миграции для перехода к Базе данных Azure для MariaDB](/azure/mariadb/howto-migrate-dump-restore)