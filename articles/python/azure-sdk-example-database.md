---
title: Подготовка базы данных Azure MySQL с помощью библиотек Azure SDK
description: Использование библиотек управления в библиотеках Azure SDK для Python, чтобы подготавливать базы данных Azure MySQL, PostgresSQL или MariaDB.
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: b1f04dbf2fa12aeab58a05191319a27072db6d28
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764678"
---
# <a name="example-use-the-azure-libraries-to-provision-a-database"></a>Пример Использование библиотек Azure для подготовки базы данных

В этом примере показано, как использовать библиотеки управления пакета Azure SDK в скрипте Python для подготовки базы данных MySQL Azure. Здесь также показан простой скрипт для запроса в базу данных с помощью библиотеки mysql-connector (не входит в пакет SDK для Azure). ([Эквивалентные команды Azure CLI](#for-reference-equivalent-azure-cli-commands) приведены далее в этой статье. Если вы предпочитаете использовать портал Azure, ознакомьтесь со статьей [Создание сервера PostgreSQL](/azure/postgresql/quickstart-create-server-database-portal) или [Создание сервера MariaDB](/azure/mariadb/quickstart-create-mariadb-server-database-using-azure-portal).)

Для инициализации базы данных PostgreSQL или MariaDB можно использовать аналогичный код.

Все описанные в этой статье команды работают одинаково как в Bash для Linux или macOS, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Установка необходимых пакетов библиотеки Azure

Создайте файл *requirements.txt* со следующим содержимым.

```text
azure-mgmt-resource==10.2.0
azure-mgmt-rdbms
azure-cli-core
mysql
mysql-connector
```

Требования относительно определенной версии azure-mgmt-resource позволяют обеспечить использование версии, совместимой с текущей версией azure-mgmt-web. Эти версии не основаны на azure.core, поэтому в них используются старые методы проверки подлинности.

В окне терминала или в командной строке с активированной виртуальной средой установите необходимые компоненты.

```cmd
pip install -r requirements.txt
```

> [!NOTE]
> В Windows попытка установить библиотеку mysql в 32-разрядную библиотеку Python приведет к ошибке из-за файла *mysql.h*. В этом случае установите 64-разрядную версию Python и повторите попытку.

## <a name="3-write-code-to-provision-the-database"></a>3: Написание кода для подготовки базы данных

Создайте файл Python с именем *provision_db.py* с приведенным ниже содержимым. Подробные объяснения даны в комментариях.

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# For PostgreSQL, use the PostgreSQLManagement class.
# For MariaDB, use the MariaDBManagement class.
from azure.mgmt.rdbms.mysql import MySQLManagementClient

from azure.mgmt.rdbms.mysql.models import ServerForCreate, ServerPropertiesForDefaultCreate, ServerVersion

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-DB-rg'
LOCATION = "westus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


# Step 2: Provision the database server

# We use a random number to create a reasonably unique database server name.
# If you've already provisioned a database and need to re-run the script, set
# the DB_SERVER_NAME environment variable to that name instead.
#
# Also set DB_USER_NAME and DB_USER_PASSWORD variables to avoid using the defaults.

db_server_name = os.environ.get("DB_SERVER_NAME", f"PythonAzureExample-MySQL-{random.randint(1,100000):05}")
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

# Obtain the management client object
mysql_client = get_client_from_cli_profile(MySQLManagementClient)

# Provision the server and wait for the result
poller = mysql_client.servers.create(RESOURCE_GROUP_NAME,
    db_server_name,
    ServerForCreate(
        location=LOCATION,
        properties=ServerPropertiesForDefaultCreate(
            administrator_login=db_admin_name,
            administrator_login_password=db_admin_password,
            version=ServerVersion.five_full_stop_seven
        )
    )
)

server = poller.result()

print(f"Provisioned MySQL server {server.name}")

# Step 3: Provision a firewall rule to allow the local workstation to connect

RULE_NAME = "allow_ip"
ip_address = os.environ["PUBLIC_IP_ADDRESS"]

# For the above code, create an environment variable named PUBLIC_IP_ADDRESS that
# contains your workstation's public IP address as reported by a site like
# https://whatismyipaddress.com/.

# Provision the rule and wait for completion
poller = mysql_client.firewall_rules.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, RULE_NAME,
    ip_address,  # Start ip range
    ip_address   # End ip range
)

firewall_rule = poller.result()

print(f"Provisioned firewall rule {firewall_rule.name}")

# Step 4: Provision a database on the server

DB_NAME = "example-db1"

poller = mysql_client.databases.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, DB_NAME)

db_result = poller.result()

print(f"Provisioned MySQL database {db_result.name} with ID {db_result.id}")
```

Чтобы запустить этот пример, необходимо создать переменную среды с именем `PUBLIC_IP_ADDRESS` и IP-адресом вашей рабочей станции.

Этот код использует методы проверки подлинности на основе CLI (`get_client_from_cli_profile`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте, лучше использовать `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности с использованием библиотек управления Azure для Python](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)
- [MySQLManagementClient (azure.mgmt.rdbms.mysql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.mysqlmanagementclient)
- [ServerForCreate (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverforcreate)
- [ServerPropertiesForDefaultCreate (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverpropertiesfordefaultcreate)
- [ServerVersion (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverversion)

См. также:
    - [PostgreSQLManagementClient (azure.mgmt.rdbms.postgresql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.postgresql.postgresqlmanagementclient)
    - [MariaDBManagementClient (azure.mgmt.rdbms.mariadb)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mariadb.mariadbmanagementclient)

## <a name="4-run-the-script"></a>4: Выполнение скрипта

```cmd
python provision_db.py
```

## <a name="5-insert-a-record-and-query-the-database"></a>5: Вставка записи и запрос в базу данных

Создайте файл с именем *use_db.py* с указанным ниже кодом. Обратите внимание на зависимости от переменных среды `DB_SERVER_NAME`, `DB_ADMIN_NAME` и `DB_ADMIN_PASSWORD`, которые должны быть заполнены значениями из кода подготовки.

Этот код работает только для MySQL; для PostgreSQL и MariaDB используются другие библиотеки.

```python
import os
import mysql.connector

db_server_name = os.environ["DB_SERVER_NAME"]
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

DB_NAME = "example-db"

connection = mysql.connector.connect(user=f"{db_admin_name}@{db_server_name}",
    password=db_admin_password, host=f"{db_server_name}.mysql.database.azure.com", port=3306,
    database=DB_NAME)

cursor = connection.cursor()

table_name = "ExampleTable1"

sql_create = f"CREATE TABLE {table_name} (name varchar(255), code int)"

cursor.execute(sql_create)
print(f"Successfully created table {table_name}")

sql_insert = f"INSERT INTO {table_name} (name, code) VALUES ('Azure', 1)"
insert_data = "('Azure', 1)"

cursor.execute(sql_insert)
print("Successfully inserted data into table")

sql_select_values= f"SELECT * FROM {table_name}"

cursor.execute(sql_select_values)
row = cursor.fetchone()

while row:
    print(str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()

connection.commit()
```

Везде в этом коде используется API mysql.connector. Единственной частью, относящейся к Azure, является основной домен узла для сервера MySQL (mysql.database.azure.com).

Затем выполните этот код:

```cmd
python use_db.py
```

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

```azurecli
az group delete -n PythonAzureExample-DB-rg  --no-wait
```

Если вам не нужны ресурсы, созданные при работе с этим примером, выполните приведенную ниже команду, чтобы не нести расходы по подписке.

[!INCLUDE [resource_group_begin_delete](includes/resource-group-begin-delete.md)]

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в скрипте Python. Для базы данных PostgreSQL используйте команды [`az postgres`](/cli/azure/postgres). Для MariaDB используйте команды [`az mariadb`](/cli/azure/mariadb).

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 ^
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 ^
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 \
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 \
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

---

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
- [Пример. Подготовка и развертывание веб-приложения](azure-sdk-example-web-app.md)
