---
author: judubois
ms.date: 05/18/2020
ms.author: judubois
ms.openlocfilehash: b1dd65024158f622d6b202dc2ad83a31f8d98296
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507722"
---
## <a name="prepare-the-working-environment"></a>Подготовка среды выполнения

Сначала настройте некоторые переменные среды с помощью следующих команд:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Замените заполнители следующими значениями, которые используются в этой статье:

- `<YOUR_DATABASE_NAME>`: Имя сервера Базы данных SQL Azure. Оно должно быть уникальным в Azure.
- `<YOUR_AZURE_REGION>`: регион Azure, который вы будете использовать. Вы можете использовать `eastus` по умолчанию, но мы рекомендуем настроить регион, расположенный близко к месту проживания. Полный список доступных регионов можно получить, введя `az account list-locations`.
- `<AZ_SQL_SERVER_PASSWORD>`: Пароль для сервера Базы данных SQL Azure. Такой пароль должен содержать не менее восьми символов и включать знаки всех следующих типов: прописные латинские буквы, строчные латинские буквы, цифры (0–9) и небуквенно-цифровые знаки (!, $, #, % и т. д.).
- `<YOUR_LOCAL_IP_ADDRESS>`: IP-адрес локального компьютера, с которого будет запускаться приложение Spring Boot. Одним из удобных способов его поиска является ввод в обозревателе адреса [whatismyip.akamai.com](http://whatismyip.akamai.com/).

Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Мы используем служебную программу `jq`, чтобы отобразить данные JSON и сделать их более удобочитаемыми. Эта программа устанавливается по умолчанию в [Azure Cloud Shell](https://shell.azure.com/). Если вам не нравится эта служебная программа, вы можете безопасно удалить `| jq` часть всех команд, которые мы будем использовать.

## <a name="create-an-azure-sql-database-instance"></a>Создание экземпляра Базы данных SQL Azure

Для начала мы создадим управляемый сервер Базы данных SQL Azure.

> [!NOTE]
> См. сведения о создании сервера Базы данных SQL Azure в руководстве по [ созданию отдельной базы данных в Базе данных SQL Azure](/azure/sql-database/sql-database-single-database-get-started).

Выполните следующую команду в [Azure Cloud Shell](https://shell.azure.com/):

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

Эта команда создает сервер Базы данных SQL Azure.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Настройка правила брандмауэра для сервера Базы данных SQL Azure

Экземпляры Базы данных SQL Azure по умолчанию защищены. В них включен брандмауэр, который блокирует все входящие подключения. Чтобы вы могли использовать нашу базу данных, добавьте правило брандмауэра, которое разрешит локальному IP-адресу обращаться к серверу базы данных.

Так как вы настроили локальный IP-адрес ранее, вы можете открыть брандмауэр сервера, выполнив следующую команду:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Настройка базы данных SQL Azure

Сервер Базы данных SQL Azure, который вы создали ранее, сейчас пустой. На нем отсутствует база данных, которую можно использовать с приложением Spring Boot. Создайте базу данных с именем `demo`, выполнив следующую команду:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```
