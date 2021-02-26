---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 0edd926e2acf016eb6389fe58c7f1d5e78628dbf
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004724"
---
## <a name="create-an-azure-database-for-mysql-resource-with-azure-cli"></a>Создание ресурса Базы данных Azure для MySQL с помощью Azure CLI

Используйте команду Azure CLI [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новый ресурс MySQL для своей базы данных. 

```azurecli
az mysql server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOURRESOURCENAME \
    --location eastus \
    --admin-user YOUR-ADMIN-NAME \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

Выполнение этой команды может занять несколько минут. В результате будет создан общедоступный ресурс в регионе `eastus`. 

Ответ содержит сведения о конфигурации сервера, включая: 
* автоматически созданный пароль для учетной записи администратора;
* команду для подключения к серверу с помощью MySQL CLI.

```text
{
  "additionalProperties": {},
  "administratorLogin": "mySqlAdmin",
  "byokEnforcement": "Disabled",
  "connectionString": "mysql defaultdb --host mysqldb-2.mysql.database.azure.com --user mySqlAdmin@mysqldb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T16:48:01.673000+00:00",
  "fullyQualifiedDomainName": "mysqldb-2.mysql.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DBforMySQL/servers/mysqldb-2",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "westus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "mysqldb-2",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "my-resource-group",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

Прежде чем подключаться к серверу с помощью программных средств, необходимо настроить правила брандмауэра, чтобы разрешить IP-адрес клиента. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mysql-resource"></a>Добавление правила брандмауэра для IP-адреса клиента в ресурс MySQL

По умолчанию правила брандмауэра не настроены. Вам нужно добавить IP-адрес клиента, чтобы установить клиентское подключение к серверу с помощью JavaScript.

```azurecli
az mysql server firewall-rule create \
--resource-group YOUR-RESOURCE-GROUP \
--server YOURRESOURCENAME \
--name AllowMyIP --start-ip-address 123.123.123.123 \
--end-ip-address 123.123.123.123
```

Если вы не знаете свой IP-адрес клиента, используйте один из следующих методов:
* используйте портал Azure для просмотра и изменения правил брандмауэра, в том числе для добавления обнаруженного IP-адреса клиента;
* выполните код Node.js, и ошибка, возникающая при нарушении правил брандмауэра, будет включать IP-адрес клиента.

Хотя вы можете добавить полный диапазон адресов в Интернете в качестве правила брандмауэра (0.0.0.0–255.255.255.255), так ваш сервер будет уязвимым для атак. 

## <a name="create-a-database-on-the-server-with-azure-cli"></a>Создание базы данных на сервере с помощью Azure CLI

Используйте команду Azure CLI [az mysql db create](/cli/azure/mysql/db#az_mysql_db_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новую базу MySQL на своем сервере. 

```azurecli
az mysql db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```


## <a name="get-the-mysql-connection-string-with-azure-cli"></a>Получение строки подключения MySQL с помощью Azure CLI

Получите строку подключения MySQL для этого экземпляра с помощью команды [az mysql server show-connection-string](/cli/azure/mysql/server#az_mysql_server_show_connection_string):

```azurecli
az mysql server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

Эта команда возвращает строки подключения для популярных языков в виде объекта JSON. Прежде чем использовать строку подключения, нужно заменить `{database}`, `{username}` и `{password}` своими значениями. 

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mysql.database.azure.com; Port=3306; Database={database}; Uid={username}@YOURRESOURCENAME; Pwd={password}",
    "jdbc": "jdbc:mysql://YOURRESOURCENAME.mysql.database.azure.com:3306/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "mysql_cmd": "mysql {database} --host YOURRESOURCENAME.mysql.database.azure.com --user {username}@YOURRESOURCENAME --password={password}",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mysql.database.azure.com', user: '{username}@YOURRESOURCENAME',password: {password}, database: {database}, port: 3306});",
    "php": "host=YOURRESOURCENAME.mysql.database.azure.com port=3306 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "python": "cnx = mysql.connector.connect(user='{username}@YOURRESOURCENAME', password='{password}', host='YOURRESOURCENAME.mysql.database.azure.com', port=3306, database='{database}')",
    "ruby": "client = Mysql2::Client.new(username: '{username}@YOURRESOURCENAME', password: '{password}', database: '{database}', host: 'YOURRESOURCENAME.mysql.database.azure.com', port: 3306)"
  }
}
``` 