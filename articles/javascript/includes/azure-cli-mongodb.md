---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: b2877a43eda86868eb3ee2841cedfd8c4d242340
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019093"
---
## <a name="create-a-cosmos-db-resource-for-mongodb"></a>Создание ресурса Cosmos DB для MongoDB

Используйте команду Azure CLI [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) в [Azure Cloud Shell](https://shell.azure.com), чтобы создать новый ресурс Cosmos DB для базы данных MongoDB. 

```azurecli
az cosmosdb create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --locations regionName=eastus \
    --kind MongoDB \
    --enable-public-network true \
    --ip-range-filter 123.123.123.123 
```

Замените `123.123.123.123` IP-адресом своего клиента или удалите параметр. 

Выполнение этой команды может занять несколько минут. В результате будет создан общедоступный ресурс в регионе `eastus`. 

```text
{
  "apiProperties": {
    "serverVersion": "3.6"
  },
  "capabilities": [
    {
      "name": "EnableMongo"
    }
  ],
  "connectorOffer": null,
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "cors": [],
  "databaseAccountOfferType": "Standard",
  "disableKeyBasedMetadataWriteAccess": false,
  "documentEndpoint": "https://mongo-2.documents.azure.com:443/",
  "enableAnalyticalStorage": false,
  "enableAutomaticFailover": false,
  "enableCassandraConnector": null,
  "enableFreeTier": false,
  "enableMultipleWriteLocations": false,
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "mongodb-2",
      "locationName": "East US"
    }
  ],
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/mongo-2",
  "ipRules": [
    {
      "ipAddressOrRange": "123.123.123.123"
    }
  ],
  "isVirtualNetworkFilterEnabled": false,
  "keyVaultKeyUri": null,
  "kind": "MongoDB",
  "location": "Central US",
  "locations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "name": "mongo-2",
  "privateEndpointConnections": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "readLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "my-resource-group",
  "systemData": {
    "createdAt": "2021-02-08T20:21:05.9519342Z"
  },
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "virtualNetworkRules": [],
  "writeLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ]
}
```

## <a name="add-firewall-rule-for-your-client-ip-address-to-mongodb-resource"></a>Добавление правила брандмауэра для IP-адреса клиента в ресурс MongoDB

По умолчанию правила брандмауэра не настроены. Вам нужно добавить IP-адрес клиента, чтобы установить клиентское подключение к серверу с помощью JavaScript.

Чтобы обновить правила брандмауэра, используйте команду [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update).

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123
```

Чтобы настроить несколько IP-адресов, используйте список с разделителями-запятыми.

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123,456.456.456.456
```

## <a name="get-the-mongodb-connection-string-for-your-resource"></a>Получение строки подключения MongoDB для ресурса

Получите строку подключения MongoDB для этого экземпляра с помощью команды [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list):

```azurecli
az cosmosdb keys list \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --type connection-strings 
```

Эта команда возвращает четыре строки подключения: две для чтения и записи и две только для чтения. Есть два способа, с помощью которых можно предоставить строку подключения двум разным системам или разработчикам для использования по отдельности. 

Подключитесь к базе данных MongoDB, используя строку подключения. Убедитесь, что для службы настроен один из следующих вариантов:

* общая доступность;
* параметры брандмауэра для IP-адреса вашего клиента.

## <a name="configure-your-azure-web-app-with-the-connection-string"></a>Настройка веб-приложения Azure с помощью строки подключения

Добавьте переменную среды **MONGODB_URL** веб-приложения Azure с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set), чтобы веб-приложение подключилось к ресурсу Cosmos DB:

```azurecli
az webapp config appsettings set \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --settings MONGODB_URL=YOUR-CONNECTION-STRING
```
