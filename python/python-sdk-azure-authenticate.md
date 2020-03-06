---
title: Проверка подлинности приложений с использованием библиотек управления Azure для Python
description: Проверка подлинности приложения Python с помощью служб Azure и библиотек SDK для управления Azure
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: abf1b0b37d782addfd2b772fb7d382ce2815e891
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709821"
---
# <a name="authenticate-by-using-the-azure-management-libraries-for-python"></a>Проверка подлинности с использованием библиотек управления Azure для Python

Из этой статьи вы узнаете, как использовать библиотеки управления из пакета SDK для Python для проверки подлинности приложения с помощью Azure Active Directory (Azure AD) и субъекта-службы. Субъект-служба — это удостоверение для приложения, зарегистрированного в Azure AD. С этим удостоверением приложение может получать доступ к ресурсам и изменять их в соответствии с разрешениями.

Чтобы зарегистрировать приложения, необходимо сначала создать Active Directory с соответствующим арендатором для вашей организации. Это можно сделать, следуя инструкциям из раздела о [создании арендатора в Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). Создав Active Directory, зарегистрируйте приложение, следуя инструкциям из статьи о [создании приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](/azure/active-directory/develop/howto-create-service-principal-portal). Затем [получите идентификаторы арендатора и приложения (клиента) для субъекта-службы](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) и настройте [секрет приложения](/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret), который будет использоваться для проверки подлинности из кода Python.

Получив эти значения, вы можете использовать эти учетные данные для проверки подлинности несколькими способами с помощью библиотек SDK для Python. В результате использования любого метода вы получите клиентский объект пакета SDK, который будете использовать для доступа к другим ресурсам из кода.

Мы настоятельно рекомендуем хранить идентификатор арендатора, идентификатор клиента и секретный код в [Azure Key Vault](/azure/key-vault/), а не в вашей системе или системе управления версиями. При необходимости вы сможете легко получить эти значения.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="mgmt-auth-file"></a>Проверка подлинности с использованием JSON-файла

С помощью этого метода вы создадите файл JSON, содержащий необходимые учетные данные для субъекта-службы, а затем — клиентский объект пакета SDK с использованием информации из файла.

1. Создайте JSON-файл (с любым именем, например *app_credentials.json*) в следующем формате. Укажите вместо четырех заполнителей идентификатор подписки Azure, идентификатор арендатора Azure AD, идентификатор приложения (клиента) и секрет:

    ```json
    {
        "subscriptionId": "<azure_aubscription_id>",
        "tenantId": "<tenant_id>",
        "clientId": "<application_id>",
        "clientSecret": "<application_secret>",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com/",
        "activeDirectoryGraphResourceId": "https://graph.windows.net/",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

    > [!TIP]
    > Вы можете получить файл с учетными данными и уже указанным идентификатором подписки. Для этого войдите в Azure, выполнив команду [az login](/cli/azure/reference-index#az-login), а затем — команду [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):
    >
    > ```azurecli
    > az login
    > az ad sp create-for-rbac --sdk-auth > credentials.json
    > ```
    >
    > После этого вы можете заменить общие значения `tenantId`, `clientId` и `clientSecret` значениями для своего приложения.

1. Сохраните этот файл в безопасном расположении, доступном для вашего кода.

1. С помощью метода [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-) создайте клиентский объект, указав вместо `<path_to_file>` путь к JSON-файлу:

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.compute import ComputeManagementClient

    client = get_client_from_auth_file(ComputeManagementClient, auth_path=<path_to_file>)
    ```

1. Либо можете сохранить путь к файлу в переменную среды с именем `AZURE_AUTH_LOCATION` и опустить аргумент `auth_path`.

## <a name="authenticate-with-a-json-dictionary"></a>Проверка подлинности с использованием словаря JSON

Чтобы не использовать файл, описанный в предыдущем разделе, вы можете создать необходимый объект JSON в переменной и вызвать [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-). В этом случае идентификатор арендатора, идентификатор клиента и секрет должны всегда храниться в безопасном расположении, например [Azure Key Vault](/azure/key-vault/).

```python
   from azure.common.client_factory import get_client_from_auth_file
   from azure.mgmt.compute import ComputeManagementClient

    # Retrieve tenant_id, client_id, and client_secret from Azure KeyVault

   config_dict = {
       "subscriptionId": "bfc42d3a-65ca-11e7-95cf-ecb1d756380e",
        "tenantId": tenant_id,
       "clientId": client_id,
       "clientSecret": client_secret,
       "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
       "resourceManagerEndpointUrl": "https://management.azure.com/",
       "activeDirectoryGraphResourceId": "https://graph.windows.net/",
       "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
       "galleryEndpointUrl": "https://gallery.azure.com/",
       "managementEndpointUrl": "https://management.core.windows.net/"
   }
   client = get_client_from_json_dict(ComputeManagementClient, config_dict)
```

## <a name="mgmt-auth-token"></a>Проверка подлинности с использованием учетных данных токена

Если вы получили учетные данные из безопасного хранилища, например [Azure Key Vault](/azure/key-vault/), сначала создайте объект [ServicePrincipalCredentials], а затем создайте экземпляр клиента, используя эти учетные данные и идентификатор подписки:

```python
from azure.mgmt.compute import ComputeManagementClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve credentials from secure storage. Never hard-code credentials into code.

credentials = ServicePrincipalCredentials(tenant = <tenant_id>,
    client_id = <client_id>, secret = <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

Чтобы обеспечить дополнительный контроль, используйте [Библиотеку проверки подлинности Azure Active Directory (ADAL) для Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) и программу-оболочку ADAL для SDK.

```python
from azure.mgmt.compute import ComputeManagementClient
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve credentials from secure storage. Never hard-code credentials into code.

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)

credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, <client_id>, <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

> [!NOTE]
> Если используется национальное облако Azure, при создании клиента управления необходимо указать соответствующий базовый URL-адрес (используя константу в `msrestazure.azure_cloud`).
>
> ```python
> client = ComputeManagementClient(credentials, subscription_id,
>     base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
> ```

### <a name="mgmt-auth-legacy"></a>Проверка подлинности с использованием учетных данных токена (не рекомендуется)

До появления [Библиотеки проверки подлинности Azure Active Directory для Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) использовался класс `UserPassCredentials`. Использование этого класса считается устаревшим методом, и мы не рекомендуем его применять, так как он не поддерживает двухфакторную проверку подлинности.

```python
from azure.common.credentials import UserPassCredentials

# DEPRECATED - legacy purposes only - use ADAL instead
credentials = UserPassCredentials(
    'user@domain.com',
    'my_smart_password'
)
```

## <a name="mgmt-auth-msi"></a>Аутентификация с помощью управляемых удостоверений Azure

Использование управляемого удостоверения Azure — это простой способ проверки подлинности ресурсов в Azure без применения определенных учетных данных.

Для использования управляемых удостоверений нужно подключиться к Azure из другого ресурса Azure, такого как функция или виртуальная машина Azure. См. подробнее об управляемых удостоверениях для ресурсов Azure в руководствах по [настройке](/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) и [использованию управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in).

```python
from msrestazure.azure_active_directory import MSIAuthentication
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

# Create MSI Authentication
credentials = MSIAuthentication()

# Create a Subscription Client
subscription_client = SubscriptionClient(credentials)
subscription = next(subscription_client.subscriptions.list())
subscription_id = subscription.subscription_id

# Create a Resource Management client
client = ResourceManagementClient(credentials, subscription_id)

# List resource groups as an example. The only limit is what role and policy are assigned to this MSI token.
for resource_group in resource_client.resource_groups.list():
    print(resource_group.name)
```

## <a name="mgmt-auth-cli"></a>Проверка подлинности на основе CLI (только для разработки)

Пакет SDK позволяет создать клиент, используя активную подписку Azure CLI, когда вы выполните команду `az login`. Для пакета SDK используется идентификатор подписки по умолчанию. Либо вы можете указать подписку, используя команду [az account](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli).

Этот вариант нужно использовать только для разработки.

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_cli_profile(ComputeManagementClient)
```
