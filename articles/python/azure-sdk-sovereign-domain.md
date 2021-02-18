---
title: Подключение ко всем регионам с помощью библиотек Azure для многооблачных решений Python
description: Использование модуля azure_cloud из msrestazure для подключения к Azure в разных независимых регионах
ms.date: 11/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c5d074a8e775fc1766df1ab8c4ed73aabc333fcc
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004758"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>Многооблачные решения. Подключение ко всем регионам с помощью библиотек Azure для Python

Библиотеки Azure для Python можно использовать для подключения ко всем регионам с [доступом](https://azure.microsoft.com/regions/services) к Azure.

По умолчанию библиотеки Azure настраиваются для подключения к глобальному облаку Azure.

## <a name="using-pre-defined-sovereign-cloud-constants"></a>Использование предварительно определенных констант национального облака

Предварительно определенные константы национального облака предоставляются модулем `azure_cloud` библиотеки `msrestazure` (версии 0.4.11 и выше):

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

Чтобы использовать определение, импортируйте соответствующую константу из `msrestazure.azure_cloud` и примените ее при создании клиентских объектов. 

При использовании `DefaultAzureCredential`, как показано в приведенном ниже примере, необходимо также использовать соответствующее значение из `CLOUD.endpoints.active_directory`.

```python
import os
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD as CLOUD
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential

# Assumes the subscription ID and tenant ID to use are in the AZURE_SUBSCRIPTION_ID and
# AZURE_TENANT_ID environment variables
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]

# When using sovereign domains (that is, any cloud other than AZURE_PUBLIC_CLOUD),
# you must use an authority with DefaultAzureCredential.
credential = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)

resource_client = ResourceManagementClient(
    credential, subscription_id,
    base_url=CLOUD.endpoints.resource_manager,
    credential_scopes=[CLOUD.endpoints.resource_manager + "/.default"])

subscription_client = SubscriptionClient(
    credential,
    base_url=CLOUD.endpoints.resource_manager,
    credential_scopes=[CLOUD.endpoints.resource_manager + "/.default"])
```
  
## <a name="using-your-own-cloud-definition"></a>Использование собственного определения облака

Следующий код использует `get_cloud_from_metadata_endpoint` с конечной точкой Azure Resource Manager для частного облака (например, на основе Azure Stack).

```python
import os
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential
from azure.profiles import KnownProfiles

# Assumes the subscription ID and tenant ID to use are in the AZURE_SUBSCRIPTION_ID and
# AZURE_TENANT_ID environment variables
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")

# When using a private cloud, you must use an authority with DefaultAzureCredential.
# The active_directory endpoint should be a URL like https://login.microsoftonline.com.
credential = DefaultAzureCredential(authority=stack_cloud.endpoints.active_directory, tenant_id=tenant_id)

resource_client = ResourceManagementClient(
    credential, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager,
    profile=KnownProfiles.v2019_03_01_hybrid,
    credential_scopes=[stack_cloud.endpoints.active_directory_resource_id + "/.default"])

subscription_client = SubscriptionClient(
    credential,
    base_url=stack_cloud.endpoints.resource_manager,
    profile=KnownProfiles.v2019_03_01_hybrid,
    credential_scopes=[stack_cloud.endpoints.active_directory_resource_id + "/.default"])
```
