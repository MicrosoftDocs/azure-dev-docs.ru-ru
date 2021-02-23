---
title: Использование JavaScript в Azure PostgreSQL
description: Для создания или перемещения базы данных PostgreSQL в Azure необходим ресурс PostgreSQL.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2633a8b9e8f120a23af7840097c2930127e294ec
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005017"
---
# <a name="develop-a-javascript-application-with-postgresql-on-azure"></a>Разработка приложения JavaScript с PostgreSQL в Azure

Для создания, перемещения или использования базы данных PostgreSQL в Azure необходим ресурс **Базы данных Azure для сервера PostgreSQL**. Узнайте, как создать ресурс и использовать свою базу данных.

## <a name="create-an-azure-database-for-postgresql-resource"></a>Создание ресурса Базы данных Azure для PostgreSQL 

Создайте ресурс с помощью следующих средств:

* [Azure CLI](../with-azure-cli/create-postgresql-server-resource.md)
* [Visual Studio Code](../with-visual-studio-code/create-azure-database.md#create-a-postgresql-database)
* [Портал Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)
* [@azure/arm-postgresql](https://www.npmjs.com/package/@azure/arm-postgresql)

## <a name="view-and-use-your-postgresql-server-on-azure"></a>Использование сервера PostgreSQL и просмотр его данных в Azure
При разработке базы данных PostgreSQL с помощью JavaScript используйте одно из следующих средств:

* [Azure Cloud Shell](https://shell.azure.com/) — доступен CLI psql.
* [pgAdmin.](https://www.pgadmin.org/)
* [Расширение](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) Visual Studio Code.

## <a name="use-sdk-packages-to-develop-your-postgresql-server-on-azure"></a>Использование пакетов SDK для разработки сервера PostgreSQL в Azure

Azure PostgreSQL использует уже доступные пакеты npm, например:

* [pg](https://www.npmjs.com/package/pg)

## <a name="use-pg-sdk-to-connect-to-postgresql-on-azure"></a>Использование пакета SDK pg для подключения к PostgreSQL в Azure

Чтобы подключить и использовать PostgreSQL в Azure с помощью JavaScript, выполните следующую процедуру:

1. Убедитесь, что установлены Node.js и npm.
1. Создайте проект Node.js в новой папке:

    ```bash
    mkdir DbDemo && \
        cd DbDemo && \
        npm init -y && \
        npm install pg && \
        touch index.js && \
        code .
    ```

    Эта команда выполняет следующие действия:
    * создает папку проекта с именем `DbDemo`;
    * выполняет переход в терминале Bash к этой папке;
    * инициализирует проект, что приводит к созданию файла `package.json`;
    * устанавливает пакет npm pg для использования async/await;
    * создает файл скрипта `index.js`;
    * открывает проект в Visual Studio Code.

1. Скопируйте следующий код JavaScript в файл `index.js`:

    ```nodejs
    const { Client } = require('pg')
    
    const query = async (connectionString) => {
        
        // create connection
        const connection = new Client(connectionString);
        connection.connect();
        
        // show tables in the postgres database
        const tables = await connection.query('SELECT table_name FROM information_schema.tables where table_type=\'BASE TABLE\';');
        console.log(tables.rows);
    
        // show users configured for the server
        const users = await connection.query('select pg_user.usename FROM pg_catalog.pg_user;');
        console.log(users.rows);
        
        // close connection
        connection.end();
    }
    
    const server='YOURRESOURCENAME';
    const user='YOUR-ADMIN-USER';
    const password='YOUR-PASSWORD';
    const database='postgres';

    const connectionString = `postgres://${user}@${server}:${password}@${server}.postgres.database.azure.com:5432/${database}`;
    
    query(connectionString)
    .then(() => console.log('done'))
    .catch((err) => console.log(err));
    ```

1. В скрипте для вашей строки подключения укажите для `YOUR-ADMIN-USER`, `YOURRESOURCENAME` и `YOUR-PASSWORD` свои значения. 

1. Выполните скрипт, чтобы подключиться к серверу `postgres` и просмотреть базовые таблицы и пользователей.

    ```bash
    node index.js
    ```

1. Просмотрите результаты. 

    ```bash
    [
      { table_name: 'pg_statistic' },
      { table_name: 'pg_type' },
      { table_name: 'pg_authid' },
      { table_name: 'pg_user_mapping' },
      { table_name: 'pg_attribute' },
      { table_name: 'pg_proc' },
      { table_name: 'pg_class' },
      { table_name: 'pg_attrdef' },
      { table_name: 'pg_constraint' },
      { table_name: 'pg_inherits' },
      { table_name: 'pg_index' },
      { table_name: 'pg_operator' },
      { table_name: 'pg_opfamily' },
      { table_name: 'pg_opclass' },
      { table_name: 'pg_am' },
      { table_name: 'pg_amop' },
      { table_name: 'pg_amproc' },
      { table_name: 'pg_language' },
      { table_name: 'pg_largeobject_metadata' },
      { table_name: 'pg_aggregate' },
      { table_name: 'pg_rewrite' },
      { table_name: 'pg_largeobject' },
      { table_name: 'pg_trigger' },
      { table_name: 'pg_event_trigger' },
      { table_name: 'pg_description' },
      { table_name: 'pg_cast' },
      { table_name: 'pg_enum' },
      { table_name: 'pg_namespace' },
      { table_name: 'pg_conversion' },
      { table_name: 'pg_depend' },
      { table_name: 'pg_database' },
      { table_name: 'pg_db_role_setting' },
      { table_name: 'pg_tablespace' },
      { table_name: 'pg_pltemplate' },
      { table_name: 'pg_auth_members' },
      { table_name: 'pg_shdepend' },
      { table_name: 'pg_shdescription' },
      { table_name: 'pg_ts_config' },
      { table_name: 'pg_ts_config_map' },
      { table_name: 'pg_ts_dict' },
      { table_name: 'pg_ts_parser' },
      { table_name: 'pg_ts_template' },
      { table_name: 'pg_extension' },
      { table_name: 'pg_foreign_data_wrapper' },
      { table_name: 'pg_foreign_server' },
      { table_name: 'pg_foreign_table' },
      { table_name: 'pg_policy' },
      { table_name: 'pg_replication_origin' },
      { table_name: 'pg_default_acl' },
      { table_name: 'pg_init_privs' },
      { table_name: 'pg_seclabel' },
      { table_name: 'pg_shseclabel' },
      { table_name: 'pg_collation' },
      { table_name: 'pg_range' },
      { table_name: 'pg_transform' },
      { table_name: 'sql_features' },
      { table_name: 'sql_implementation_info' },
      { table_name: 'sql_languages' },
      { table_name: 'sql_packages' },
      { table_name: 'sql_parts' },
      { table_name: 'sql_sizing' },
      { table_name: 'sql_sizing_profiles' }
    ]
    [ { usename: 'azure_superuser' }, { usename: 'YOUR-ADMIN-USER' } ]
    done
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание веб-приложения JavaScript](../deploy-web-app.md)
* [сервер базы данных Azure для PostgreSQL](/azure/postgresql/).