---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 0c441254e2c053198cc8e41e0d2457adbf3079af
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004745"
---
## <a name="create-a-mariadb-resource-with-azure-cli"></a>Создание ресурса MariaDB с помощью Azure CLI

Используйте команду Azure CLI [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новый ресурс MariaDB для своей базы данных. 

```azurecli
az mariadb server create \
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
  "administratorLogin": "mariaAdmin",
  "connectionString": "mysql defaultdb --host mariadb-2.mariadb.database.azure.com --user mariaAdmin@mariadb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T19:14:17.317000+00:00",
  "fullyQualifiedDomainName": "mariadb-2.mariadb.database.azure.com",
  "id": "/subscriptions/bb881e62-cf77-4d5d-89fb-29d71e930b66/resourceGroups/my-resource-group/providers/Microsoft.DBforMariaDB/servers/mariadb-2",
  "location": "westus",
  "masterServerId": "",
  "name": "mariadb-2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

Прежде чем подключаться к серверу с помощью программных средств, необходимо настроить правила брандмауэра, чтобы разрешить IP-адрес клиента. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mariadb-resource"></a>Добавление правила брандмауэра для IP-адреса клиента в ресурс MariaDB

По умолчанию правила брандмауэра не настроены. Вам нужно добавить IP-адрес клиента, чтобы установить клиентское подключение к серверу с помощью JavaScript.

```azurecli
az mariadb server firewall-rule create \
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

Используйте команду Azure CLI [az mariadb db create](/cli/azure/mariadb/db#az_mariadb_db_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новую базу MariaDB на своем сервере. 

```azurecli
az mariadb db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-mariadb-connection-string-with-azure-cli"></a>Получение строки подключения MariaDB с помощью Azure CLI

Получите строку подключения MariaDB для этого экземпляра с помощью команды [az mariadb server show-connection-string](/cli/azure/mariadb/server#az_mariadb_server_show_connection_string):

```azurecli
az mariadb server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME \
    --database-name YOURDATABASENAME \
    --admin-user YOUR-ADMIN-NAME \
    --admin-password YOUR-ADMIN-PASSWORD 
```

Эта команда возвращает строки подключения для популярных языков в виде объекта JSON.

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mariadb.database.azure.com; Port=3306; Database=YOURDATABASENAME; Uid=YOUR-ADMIN-NAME@YOURRESOURCENAME; Pwd=YOUR-ADMIN-PASSWORD",
    "jdbc": "jdbc:mariadb://YOURRESOURCENAME.mariadb.database.azure.com:3306/YOURDATABASENAME?user=YOUR-ADMIN-NAME@YOURRESOURCENAME&password=YOUR-ADMIN-PASSWORD",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mariadb.database.azure.com', user: 'YOUR-ADMIN-NAME@YOURRESOURCENAME',password: YOUR-ADMIN-PASSWORD, database: YOURDATABASENAME, port: 3306});",
    "php": "host=YOURRESOURCENAME.mariadb.database.azure.com port=3306 dbname=YOURDATABASENAME user=YOUR-ADMIN-NAME@YOURRESOURCENAME password=YOUR-ADMIN-PASSWORD",
    "python": "cnx = mysql.connector.connect(user='YOUR-ADMIN-NAME@YOURRESOURCENAME', password='YOUR-ADMIN-PASSWORD', host='YOURRESOURCENAME.mariadb.database.azure.com', port=3306, database='YOURDATABASENAME')",
    "ruby": "client = Mysql2::Client.new(username: 'YOUR-ADMIN-NAME@YOURRESOURCENAME', password: 'YOUR-ADMIN-PASSWORD', database: 'YOURDATABASENAME', host: 'YOURRESOURCENAME.mariadb.database.azure.com', port: 3306)"
  }
}
``` 




