---
title: Модули Azure для JavaScript
description: Обзор модулей служб и управления Azure для JavaScript
ms.date: 06/17/2017
ms.topic: article
ms.openlocfilehash: 193e2d3c92a9c2b8e3970e7a130246947a7cc4da
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792564"
---
# <a name="azure-modules-for-javascript"></a>Модули Azure для JavaScript

Управляйте ресурсами Azure и подключайтесь к службам из приложений JavaScript с помощью модулей Azure для JavaScript. Код доступен в виде модулей npm](/api/?view=azure-node-latest.md) для использования в проектах.

## <a name="manage-azure-resources"></a>Управление ресурсами Azure

Используйте модули управления, чтобы создавать ресурсы и выполнять запросы к ним из приложений, а также создавать собственные средства автоматизации Azure.

Например, для создания виртуальной машины Linux, использующей существующий сетевой интерфейс, напишите следующий код:

```javascript
const msRestAzure = require('ms-rest-azure');
const ComputeManagementClient = require('azure-arm-compute');

// read in service principal values from env variables
const clientId = process.env['CLIENT_ID'];
const domain = process.env['DOMAIN'];
const secret = process.env['APPLICATION_SECRET'];
const subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];

msRestAzure.loginWithServicePrincipalSecret(clientId, secret, domain, function (err, credentials, subscriptions) {
    if (err) return console.log(err);
    const computeClient = new ComputeManagementClient(credentials, subscriptionId);
    // customize the VM
    const vmParameters = {
        location: "eastus",
        osProfile: {
            computerName: "newLinuxVM",
            adminUsername: adminUsername,
            adminPassword: adminPassword
        },
        linuxConfiguration: {
            ssh: {
                publicKeys: [mySshKey]
            }
        },
        hardwareProfile: {
            vmSize: 'Basic_A1'
        },
        networkProfile: {
            networkInterfaces: [
                {
                    id: myNetworkInterfaceId,
                    primary: true
                }
            ]
        },
        storageProfile: {
            imageReference: {
                publisher: 'Canonical',
                offer: 'UbuntuServer',
                sku: '16.04-LTS',
                version: 'latest'
            },
        }
    };

    // create the VM
    computeClient.virtualMachines.createOrUpdate("myResourceGroup", "newLinuxVM", vmParameters, function (err, data) {
        if (err) return console.log(err);
    });

});
```

Полный список модулей см. в [инструкциях по установке](/api/?view=azure-node-latest). Инструкции по настройке аутентификации, выполнению примера кода, а также созданию и обновлению ресурсов в подписке Azure см. в [статье по началу работы](../index.yml).

## <a name="connect-to-azure-services"></a>Подключение к службам Azure

Модули Azure можно использовать не только для создания ресурсов и управления ими в Azure, но и для подключения и использования облачных служб в приложениях. Например, для обновления табличной базы данных SQL или обновления файлов в службе хранилища Azure. Выберите пакет, который нужен для работы определенной службы, в [полном списке](/api/?view=azure-node-latest) и ознакомьтесь с руководствами и примерами кода, которые помогут вам использовать модули в приложениях, в [центре разработчиков JavaScript](https://azure.microsoft.com/develop/nodejs/).

Например, чтобы вывести содержимое каждого большого двоичного объекта в контейнере хранилища Azure, используйте следующий код:

```javascript
var azure = require('azure-storage');
var blobService = azure.createBlobService(storageConnectionString);
blobService.listBlobsSegmented('testcontainer', null, function(error, result, response) {
   if (err) return console.log(err);
   console.log(result);
});
```

## <a name="sample-code-and-reference"></a>Примеры кода и справочные материалы

В примерах кода ниже представлены общие задачи, выполняемые с помощью модулей управления Azure. Это готовый код, который вы можете использовать в своих приложениях.

- [Виртуальные машины](/samples/browse/?languages=javascript%2Cnodejs)
- [Веб-приложения](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions%2Cazure-app-service%2Cazure-logic-apps)
- [База данных SQL](/samples/browse/?languages=javascript%2Cnodejs&products=azure-cosmos-db%2Cazure-sql-database)

[Справочник](/javascript/api) по всем модулям служб и модулям управления. Сведения о новых функциях и критически важных изменениях, а также инструкции по переходу с предыдущих версий см. в [заметках о выпуске](https://github.com/Azure/azure-sdk-for-node/releases).
