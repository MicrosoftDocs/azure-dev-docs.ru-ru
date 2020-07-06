---
title: Подключение ко всем регионам с помощью библиотек Azure для многооблачных решений Python
description: Использование модуля azure_cloud из msrestazure для подключения к Azure в разных независимых регионах
ms.date: 06/09/2020
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: c8dc34260f4a37090af8c8408f7da70cf1de1f23
ms.sourcegitcommit: b3e506c6f140d91e6fdd9dcadf22ab1aa67f6978
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947524"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>Многооблачные решения. Подключение ко всем регионам с помощью библиотек Azure для Python

Библиотеки Azure для Python можно использовать для подключения ко всем регионам с [доступом](https://azure.microsoft.com/regions/services) к Azure.

По умолчанию библиотеки Azure настраиваются для подключения к глобальной службе Azure.

## <a name="using-pre-declared-cloud-definition"></a>Использование предварительно объявленного определения облака

Используйте модуль `azure_cloud` `msrestazure` (0.4.11+):

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

credentials = UserPassCredentials(login, password,
    cloud_environment=AZURE_CHINA_CLOUD)

client = ResourceManagementClient(credentials,
    subscription_id, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```
  
Ниже приведены доступные определения облаков:

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

## <a name="using-your-own-cloud-definition"></a>Использование собственного определения облака

В этом коде вы используете `get_cloud_from_metadata_endpoint` с конечной точкой Azure Resource Manager для частного облака (например, на основе Azure Stack):

```python
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")
credentials = UserPassCredentials(login, password,
    cloud_environment=stack_cloud)

client = ResourceManagementClient(credentials, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager)
```

## <a name="using-adal"></a>Использование ADAL

Для подключения к другому региону, нужно учитывать следующее:

- Какая конечная точка используется для запроса маркера (для аутентификации)?
- Какая конечная точка используется для этого маркера (для применения)?

Ниже приведен общий пример:

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Public Azure - default values
authentication_endpoint = 'https://login.microsoftonline.com/'
azure_endpoint = 'https://management.azure.com/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-government"></a>Azure для государственных организаций

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Government
authentication_endpoint = 'https://login.microsoftonline.us/'
azure_endpoint = 'https://management.usgovcloudapi.net/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-germany"></a>Azure для Германии

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure Germany
authentication_endpoint = 'https://login.microsoftonline.de/'
azure_endpoint = 'https://management.microsoftazure.de/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure China
authentication_endpoint = 'https://login.chinacloudapi.cn/'
azure_endpoint = 'https://management.chinacloudapi.cn/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```
