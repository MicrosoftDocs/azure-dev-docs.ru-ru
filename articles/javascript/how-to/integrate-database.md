---
title: Базы данных с приложениями Node.js в Azure
description: Azure предлагает несколько разных баз данных для использования с веб-приложениями и другими приложениям Node.js.
ms.topic: how-to
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2aae93a85ca505967f0c999be4addc78ac31ad02
ms.sourcegitcommit: 1901759f41adfac3c3f2ff135bcf72206543b639
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933287"
---
# <a name="integrate-databases-in-nodejs-apps"></a>Интеграция баз данных в приложения Node.js

Базы данных Azure предоставляют отличное управляемое облачное решение для обработки данных, а также собственный API и пакет SDK Azure для подключения к базе данных. 

## <a name="database-and-data-storage-solutions-on-azure"></a>Решения для хранения и баз данных в Azure

В следующей таблице приведены ссылки на различные статьи о подключении и использовании баз данных Azure с Node.js. Параллельный список различных параметров базы данных см. в разделе [Базы данных — Полностью управляемые интеллектуальные службы баз данных](https://azure.microsoft.com/product-categories/databases/).

| Служба | Краткое руководство | Пакет npm |
| --- | --- | --- |
| **SQL Server** в Cosmos DB| [Создание веб-приложения Node.js в Azure Cosmos DB с помощью SQL Server](/azure/cosmos-db/create-sql-api-nodejs) | [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos) |
| **MongoDB** в Cosmos DB| [Разработка веб-приложения на основе Node.js и MongoDB в Azure](/azure/app-service-web/app-service-web-tutorial-nodejs-mongodb-app) | любой клиент MongoDB |
| **Cassandra** в Cosmos DB|[Создание приложения Cassandra с помощью пакета SDK для Node.js и Azure Cosmos DB](/azure/cosmos-db/create-cassandra-nodejs)|[npm cassandra-driver](https://www.npmjs.com/package/cassandra-driver)|
| **Gremlin** в Cosmos DB|[Создание приложения Node.js с помощью учетной записи API Gremlin для Azure Cosmos DB](/azure/cosmos-db/create-graph-nodejs)|[npm gremlin](https://www.npmjs.com/package/gremlin)|
| **Redis Cache** в Cosmos DB| [Использование кэша Redis для Azure с Node.js](/azure/redis-cache/cache-nodejs-get-started) | [npm redis](https://www.npmjs.com/package/redis)|
| **База данных SQL Azure** | [Использование Node.js для создания запросов и базы данных SQL Azure](/azure/sql-database/sql-database-connect-query-nodejs) |[npm tedious](https://www.npmjs.com/package/tedious) |
| **MySQL** | [Подключение и обращение к данным с помощью Node.js](/azure/mysql/connect-nodejs) | [npm mysql](https://www.npmjs.com/package/mysql)|
| **PostgreSQL** | [Подключение и обращение к данным с помощью Node.js](/azure/postgresql/connect-nodejs) |[npm pg](https://www.npmjs.com/package/pg) |

## <a name="cosmos-db-connection-strings-with-azure-cli"></a>Строки подключения Cosmos DB с Azure CLI

Выполните следующую команду ([az cosmosdb keys list](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-connection-strings)):

```azurecli-interactive
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="sql-connection-strings-with-azure-cli"></a>Строки подключения SQL с Azure CLI

Выполните следующую команду ([az sql db show-connection-string](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string)):

```azurecli-interactive
az sql db show-connection-string \
    --client {ado.net, jdbc, odbc, php, php_pdo, sqlcmd} \
     [--auth-type {ADIntegrated, ADPassword, SqlPassword}] \
     [--ids] \
     [--name] \
     [--server] \
     [--subscription]
```

## <a name="mysql-username-and-password-with-azure-cli"></a>Имя пользователя и пароль MySQL с Azure CLI

Они задаются во [время создания ресурса](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create). 

## <a name="postgresql-username-and-password-with-azure-cli"></a>Имя пользователя и пароль PostgreSQL с Azure CLI

Они задаются во [время создания ресурса](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create). 

## <a name="azure-storage-solutions-for-files-and-data"></a>Решения службы хранилища Azure для файлов и данных

Службу хранилища Azure также можно использовать для хранения файлов (BLOB), таблиц и очередей (сообщений):

| Служба | Краткое руководство |Рекомендуемый пакет SDK |
| --- | --- |--- |
| **BLOB-объекты** | [Передача, скачивание, составление списков и удаление больших двоичных объектов с помощью пакета SDK версии 10 службы хранилища Azure для JavaScript](/azure/storage/blobs/storage-quickstart-blobs-nodejs-v10) |[@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob)|
| **Очереди** | [Использование хранилища очередей из Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues) |[npm @azure/storage-queue](https://www.npmjs.com/package/@azure/storage-queue)|
| **Таблицы** | [Использование табличного хранилища из Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs) |[npm azure-storage](https://www.npmjs.com/package/azure-storage)|

## <a name="next-steps"></a>Дальнейшие действия

* [Запись бессерверного кода](develop-serverless-apps.md) с помощью функций Azure