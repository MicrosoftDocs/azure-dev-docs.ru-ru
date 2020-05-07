---
title: Выбор облаков с помощью Azure CLI
description: Создание и администрирование нескольких облаков и вход в них с помощью Azure CLI.
author: dbradish-microsoft
manager: barbkess
ms.author: dbradish
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 8e24a4740d97ddf67f81e60fef9217a4e72daab0
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031226"
---
# <a name="select-clouds-with-the-azure-cli"></a>Выбор облаков с помощью Azure CLI

При работе в разных регионах или использовании [Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/) вам может понадобиться несколько облаков. Корпорация Майкрософт предоставляет доступные для использования облака в соответствии с местным законодательством в пределах каждого региона. В этой статье показано, как получить сведения об облаках, изменить текущее облако, а также зарегистрировать новые облака или отменить их регистрацию.

## <a name="list-available-clouds"></a>Получение списка доступных облаков

Вы можете отобразить список доступных облаков с помощью команды [az cloud list](/cli/azure/cloud#az-cloud-list). Эта команда показывает, какое облако сейчас используется и какой у него текущий профиль, а также отображает сведения о суффиксах регионов и именах узлов.

Получить сведения об активном облаке и полном списке доступных облаков можно так:

```azurecli-interactive
az cloud list --output table
```

```output
IsActive    Name               Profile
----------  -----------------  ---------
True        AzureCloud         latest
            AzureChinaCloud    latest
            AzureUSGovernment  latest
            AzureGermanCloud   latest
```

Активное сейчас облако обозначено значением `True` в столбце `IsActive`. В одно и то же время использоваться может только одно облако. Получить дополнительные сведения об облаке, включая описание конечных точек, используемых им для служб Azure, можно с помощью команды `cloud show`:

```azurecli-interactive
az cloud show --name AzureChinaCloud --output json
```

```json
{
  "endpoints": {
    "activeDirectory": "https://login.chinacloudapi.cn",
    "activeDirectoryDataLakeResourceId": null,
    "activeDirectoryGraphResourceId": "https://graph.chinacloudapi.cn/",
    "activeDirectoryResourceId": "https://management.core.chinacloudapi.cn/",
    "batchResourceId": "https://batch.chinacloudapi.cn/",
    "gallery": "https://gallery.chinacloudapi.cn/",
    "management": "https://management.core.chinacloudapi.cn/",
    "resourceManager": "https://management.chinacloudapi.cn",
    "sqlManagement": "https://management.core.chinacloudapi.cn:8443/",
    "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json"
  },
  "isActive": false,
  "name": "AzureChinaCloud",
  "profile": "latest",
  "suffixes": {
    "azureDatalakeAnalyticsCatalogAndJobEndpoint": null,
    "azureDatalakeStoreFileSystemEndpoint": null,
    "keyvaultDns": ".vault.azure.cn",
    "sqlServerHostname": ".database.chinacloudapi.cn",
    "storageEndpoint": "core.chinacloudapi.cn"
  }
}
```

## <a name="switch-the-active-cloud"></a>Изменение активного облака

Чтобы задать облако по умолчанию с помощью файла конфигурации, ознакомьтесь с разделом [Переменные среды и значения конфигурации CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).  Чтобы переключиться с активного сейчас облака, выполните команду [az cloud set](/cli/azure/cloud#az-cloud-set). Эта команда принимает один обязательный аргумент — имя облака.

```azurecli-interactive
az cloud set --name AzureChinaCloud
```

> [!IMPORTANT]
> Если срок действия аутентификации для активации облака истек, необходимо повторно выполнить аутентификацию, прежде чем выполнять другие задачи CLI. Если вы переключаетесь на новое облако впервые, вам также понадобится настроить активную подписку.
> Инструкции по проверке подлинности см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md). Сведения об управлении подписками см. в руководстве по [управлению подписками Azure с помощью Azure CLI](manage-azure-subscriptions-azure-cli.md).

## <a name="register-a-new-cloud"></a>Регистрация нового облака

Зарегистрируйте новое облако, если у вас есть свои конечные точки для Azure Stack. Облако создается с помощью команды [az cloud register](/cli/azure/cloud#az-cloud-register). Для этой команды требуется имя и набор конечных точек службы. Сведения о регистрации облака для использования Azure Stack см. в статье [Использование профилей версий API и Azure CLI в Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-azurecli2#connect-to-azure-stack).

Вам не нужно регистрировать сведения в регионах "Китай", "US Government" или "Германия". Эти облака управляются корпорацией Майкрософт и доступны по умолчанию.  Дополнительные сведения обо всех доступных конечных точках см. в [документации по `az cloud register`](/cli/azure/cloud#az-cloud-register).

Регистрация в облаке не подразумевает автоматическое переключение на него. Для выбора нового облака используйте команду `az cloud set`.

## <a name="update-an-existing-cloud"></a>Обновление существующего облака

При наличии соответствующих разрешений вы также можете обновить существующее облако. Обновление облака приводит к переключению на другой профиль служб Azure или изменению конечных точек подключения.
Это можно сделать с помощью команды [az cloud update](/cli/azure/cloud#az-cloud-update), которая принимает те же аргументы, что и команда `az cloud register`.

## <a name="unregister-a-cloud"></a>Отмена регистрации облака

Если созданное облако вам больше не нужно, его регистрацию можно отменить с помощью команды [az cloud unregister](/cli/azure/cloud#az-cloud-unregister):

```azurecli-interactive
az cloud unregister --name MyCloud
```
