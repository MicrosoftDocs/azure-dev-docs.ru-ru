---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 1fe0e6ef7147040d1a5496f6492cc1e63e2e425e
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100005023"
---
## <a name="create-an-azure-database-for-postgresql-server-resource-with-azure-cli"></a>Создание ресурса сервера Базы данных Azure для PostgreSQL с помощью Azure CLI

Используйте команду Azure CLI [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новый ресурс сервера PostgreSQL для своей базы данных. 

```azurecli
az postgres server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --location eastus \
    --name YOURRESOURCENAME \
    --admin-user YOUR-ADMIN-USER \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

Выполнение этой команды может занять несколько минут. В результате будет создан общедоступный ресурс в регионе `eastus`. 

Ответ содержит сведения о конфигурации сервера, включая: 
* автоматически созданный пароль для учетной записи администратора;
* строку подключения.

```text
{
  "additionalProperties": {},
  "administratorLogin": "YOUR-ADMIN-USER",
  "byokEnforcement": "Disabled",
  "connectionString": "postgres://YOUR-ADMIN-USER%40YOURRESOURCENAME:123456789@YOURRESOURCENAME.postgres.database.azure.com/postgres?sslmode=require",
  "earliestRestoreDate": "2021-02-08T21:56:20.130000+00:00",
  "fullyQualifiedDomainName": "YOURRESOURCENAME.postgres.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.DBforPostgreSQL/servers/YOURRESOURCENAME",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "eastus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "YOURRESOURCENAME",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Disabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 51200
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

Прежде чем подключаться к серверу с помощью программных средств, необходимо настроить правила брандмауэра, чтобы разрешить IP-адрес клиента. 

## <a name="add-a-firewall-rule-for-your-client-ip-address-to-postgresql-resource"></a>Добавление правила брандмауэра для IP-адреса клиента в ресурс PostgreSQL

По умолчанию правила брандмауэра не настроены. Вам нужно добавить IP-адрес клиента, чтобы установить клиентское подключение к серверу с помощью JavaScript.

Используйте команду Azure CLI [az postgres server firewall-rule create](/cli/azure/postgres/server#az_postgres_server_firewall_rule_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новое правило брандмауэра для своей базы данных. 


```azurecli
az postgres server firewall-rule create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server YOURRESOURCENAME \
    --name AllowMyIP \
    --start-ip-address 123.123.123.123 \
    --end-ip-address 123.123.123.123
```

Если вы не знаете свой IP-адрес клиента, используйте один из следующих методов:
* используйте портал Azure для просмотра и изменения правил брандмауэра, в том числе для добавления обнаруженного IP-адреса клиента;
* выполните код Node.js, и ошибка, возникающая при нарушении правил брандмауэра, будет включать IP-адрес клиента.

Хотя вы можете добавить полный диапазон адресов в Интернете в качестве правила брандмауэра (0.0.0.0–255.255.255.255), так ваш сервер будет уязвимым для атак. 

## <a name="create-a-postgresql-database-on-the-server-with-azure-cli"></a>Создание базы данных PostgreSQL на сервере с помощью Azure CLI

Используйте команду Azure CLI [az postgres db create](/cli/azure/postgres/db#az_postgres_db_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новую базу PostgreSQL на своем сервере. 

```azurecli
az postgres db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-postgresql-connection-string-with-azure-cli"></a>Получение строки подключения PostgreSQL с помощью Azure CLI

Получите строку подключения PostgreSQL для этого экземпляра с помощью команды [az postgres server show-connection-string](/cli/azure/postgres/server#az_postgres_server_show_connection_string):

```azurecli
az postgres server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

Эта команда возвращает строки подключения для популярных языков в виде объекта JSON. Прежде чем использовать строку подключения, нужно заменить `{database}`, `{username}` и `{password}` своими значениями. Замените `YOURRESOURCENAME` именем ресурса.

```json
{
  "connectionStrings": {
    "C++ (libpq)": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}YOURRESOURCENAME password={password} sslmode=require",
    "ado.net": "Server=YOURRESOURCENAME.postgres.database.azure.com;Database={database};Port=5432;User Id={username}@YOURRESOURCENAME;Password={password};",
    "jdbc": "jdbc:postgresql://YOURRESOURCENAME.postgres.database.azure.com:5432/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "node.js": "var client = new pg.Client('postgres://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com:5432/{database}');",
    "php": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "psql_cmd": "postgresql://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com/{database}?sslmode=require",
    "python": "cnx = psycopg2.connect(database='{database}', user='{username}@YOURRESOURCENAME', host='YOURRESOURCENAME.postgres.database.azure.com', password='{password}', port='5432')",
    "ruby": "cnx = PG::Connection.new(:host => 'YOURRESOURCENAME.postgres.database.azure.com', :user => '{username}@YOURRESOURCENAME', :dbname => '{database}', :port => '5432', :password => '{password}')"
  }
}
``` 