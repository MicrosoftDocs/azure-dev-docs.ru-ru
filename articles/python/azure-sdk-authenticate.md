---
title: Проверка подлинности приложений Python с помощью служб Azure
description: Как получить необходимые объекты учетных данных для аутентификации приложения Python с помощью служб Azure и с использованием библиотек Azure
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 08636d4a9b8b0b93b6e448b919a14cbfc3ae2a96
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982676"
---
# <a name="how-to-authenticate-python-apps-with-azure-services"></a>Проверка подлинности приложений Python с помощью служб Azure

При написании кода приложения с использованием библиотек Azure для Python для доступа к ресурсам Azure применяется приведенный ниже шаблон.

1. Получите учетные данные (как правило, это одноразовая операция).
1. Используйте учетные данные для получения соответствующего объекта клиента для ресурса.
1. Попытайтесь получить доступ к ресурсу или изменить его через клиентский объект, который создает HTTP-запрос к REST API ресурса.

Запрос к REST API — это точка, в которой Azure выполняет проверку подлинности удостоверения приложения, как описано в объекте учетных данных. Затем Azure проверяет, авторизовано ли это удостоверение для выполнения запрошенного действия. Если удостоверение не авторизовано, операция завершается ошибкой. (Предоставление разрешений зависит от типа ресурса, такого как Azure Key Vault, служба хранилища Azure и т. д. Дополнительные сведения см. в документации по этому типу ресурса.)

Удостоверение, участвующее в этих процессах, то есть удостоверение, описываемое в объекте учетных данных, обычно определяется *субъектом безопасности*, который представляет пользователя, группу, службу или приложение. В ряде методов проверки подлинности, описанных в этой статье, используется явный субъект, который обычно называют *субъектом-службой*.

Однако для большинства облачных приложений рекомендуется использовать объект `DefaultAzureCredential`, как описано в первом разделе, так как он полностью избавляет вас от необходимости обрабатывать субъект-службу для приложения.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="authenticate-with-defaultazurecredential"></a>Проверка подлинности с помощью метода DefaultAzureCredential

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Obtain the credential object. When run locally, DefaultAzureCredential relies
# on environment variables named AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.
credential = DefaultAzureCredential()

# Create the client object using the credential
#
# **NOTE**: SecretClient here is only an example; the same process
# applies to all other Azure client libraries.

vault_url = os.environ["KEY_VAULT_URL"]
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to retrieve a secret value. The operation fails if the principal
# cannot be authenticated or is not authorized for the operation in question.
retrieved_secret = client.get_secret("secret-name-01")
```

Класс [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) из библиотеки [`azure-identity`](/python/api/azure-identity/azure.identity?view=azure-python) обеспечивает самые простые и рекомендуемые средства проверки подлинности.

Приведенный выше код использует метод `DefaultAzureCredential` при доступе к Azure Key Vault, где URL-адрес Key Vault доступен в переменной среды с именем `KEY_VAULT_URL`. Код четко реализует шаблон, описанный в начале этой статьи: получение объекта учетных данных, создание клиентского объекта пакета SDK, а затем попытка выполнения операции с использованием этого клиентского объекта.

Опять же, проверка подлинности и авторизация не происходят до выполнения последнего шага. Для создания объекта [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) пакета SDK не требуется связь с данным ресурсом. Этот объект `SecretClient` является просто программой-оболочкой для базового REST API Azure и существует только в памяти среды выполнения приложения. Только при вызове метода [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) клиентский объект создает соответствующий вызов REST API в Azure. Затем конечная точка Azure `get_secret` выполняет проверку подлинности удостоверения вызывающей стороны и проверяет авторизацию.

Когда код развертывается и выполняется в Azure, метод `DefaultAzureCredential` автоматически использует назначенное системой (управляемое) удостоверение, которое вы можете включить для приложения в любой службе, в которой оно размещено. Например, для веб-приложения, развернутого в Службе приложений Azure, вы включаете его управляемое удостоверение с помощью параметра **Удостоверение** > **Назначено системой** на портале Azure или с помощью команды `az webapp identity assign` в Azure CLI. Разрешения для определенных ресурсов, таких как служба хранилища Azure или Azure Key Vault, также назначаются этому удостоверению с помощью портала Azure или Azure CLI. В подобных случаях это удостоверение, управляемое Azure, повышает безопасность, так как вы никогда не сталкиваетесь с явной субъектом-службой в коде.

При локальном запуске кода метод `DefaultAzureCredential` автоматически использует субъект-службу, описанную в переменных среды с именами `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` и `AZURE_CLIENT_SECRET`. Затем при вызове конечной точки API клиентский объект пакета SDK (безопасно) включает эти значения в заголовок HTTP-запроса. Изменения кода не требуются. Дополнительные сведения о создании субъекта-службы и настройке переменных среды см. в разделе [Настройка проверки подлинности](configure-local-development-environment.md#configure-authentication).

В обоих случаях используемому удостоверению должны быть назначены разрешения для соответствующего ресурса, который описан в документации для отдельных служб. Дополнительные сведения о разрешениях Key Vault, которые потребуются для приведенного выше кода, см. в статье [Обеспечение проверки подлинности Azure Key Vault с помощью политики управления доступом](/azure/key-vault/general/group-permissions-for-apps).

<a name="cli-auth-note"></a>
> [!IMPORTANT]
> В дальнейшем метод `DefaultAzureCredential` будет использовать удостоверение, которое создает подключение Azure CLI с помощью `az login`, если переменные среды субъекта-службы недоступны. Если вы являетесь владельцем или администратором подписки, практическим результатом этой функции является то, что код имеет доступ к большинству ресурсов этой подписки без необходимости назначения каких-либо специальных разрешений. Такое поведение удобно для экспериментов. Тем не менее, мы настоятельно рекомендуем использовать определенные субъект-службы и назначать определенные разрешения в начале написания рабочего кода, так как вы узнаете, как назначать точные разрешения различным удостоверениям, а также сможете точно проверить эти разрешения в тестовых средах перед развертыванием в рабочей среде.

### <a name="using-defaultazurecredential-with-sdk-management-libraries"></a>Использование метода DefaultAzureCredential с библиотеками управления пакета SDK

```python
# WARNING: this code presently fails with current release libraries!

from azure.identity import DefaultAzureCredential

# azure.mgmt.resource is an Azure SDK management library
from azure.mgmt.resource import SubscriptionClient

# Attempt to retrieve the subscription ID
credential = DefaultAzureCredential()
subscription_client = SubscriptionClient(credential)

# The following line produces a "no attribute 'signed_session'" error:
subscription = next(subscription_client.subscriptions.list())

print(subscription.subscription_id)
```

Сейчас метод `DefaultAzureCredential` работает только с клиентскими библиотеками пакета Azure SDK (плоскость данных) и предварительными версиями библиотек управления Azure SDK (последними предварительными версиями библиотек, имена которых начинаются с `azure-mgmt`), как показано в этом примере кода. Так, при использовании текущего выпуска библиотек вызов `subscription_client.subscriptions.list()` завершается сбоем с довольно неясной ошибкой: DefaultAzureCredential object has no attribute signed_session (Объект DefaultAzureCredential не имеет атрибута signed_session). Эта ошибка возникает из-за того, что текущие библиотеки управления пакета SDK предполагают, что объект учетных данных содержит свойство `signed_session`, в котором отсутствует `DefaultAzureCredential`.

Эту проблему можно решить, используя библиотеки управления предварительной версии, как описано в записи блога, посвященной [представлению новых предварительных версий для библиотек управления Azure](https://devblogs.microsoft.com/azure-sdk/introducing-new-previews-for-azure-management-libraries/).

Вы также можете использовать следующие методы:

1. Используйте любой из других методов проверки подлинности, описанных в последующих разделах этой статьи, который может подойти для кода, использующего *только* библиотеки управления пакета SDK и который не будет развернут в облаке. В этом случае вы можете полагаться только на локальные субъекты-службы.

1. Вместо `DefaultAzureCredential` используйте [класс CredentialWrapper (cred_wrapper.py)](https://gist.github.com/lmazuel/cc683d82ea1d7b40208de7c9fc8de59d), предоставленный участником команды разработки Azure SDK. Как только этап предварительной версии для обновленных библиотек управления завершится, вы сможете просто вернуться к `DefaultAzureCredential`. Преимущество этого метода заключается в том, что вы можете использовать одни и те же учетные данные для клиента SDK и для библиотек управления. Он работает как локально, так и в облаке.

    Предположим, что вы загрузили копию *cred_wrapper.py* в папку проекта. Приведенный выше код будет выглядеть следующим образом:

    ```python
    from cred_wrapper import CredentialWrapper
    from azure.mgmt.resource import SubscriptionClient

    credential = CredentialWrapper()
    subscription_client = SubscriptionClient(credential)
    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

    Опять же, как только этап предварительной версии для обновленных библиотек управления завершится, вы сможете использовать `DefaultAzureCredential` напрямую.

## <a name="other-authentication-methods"></a>Другие методы проверки подлинности

Хотя `DefaultAzureCredential` является рекомендуемым методом проверки подлинности для большинства сценариев, существуют другие методы с приведенными ниже предупреждениями.

- Большинство методов работают с явными субъектами-службами и не используют преимущества управляемого удостоверения для кода, развернутого в облаке. Таким образом, при использовании с рабочим кодом вам необходимо управлять отдельными субъектами-службами и обслуживать их для облачных приложений.

- Некоторые методы, такие как проверка подлинности на основе CLI, работают только с локальными сценариями и не могут использоваться с рабочим кодом.

Управление субъектами-службами для приложений, развернутых в облаке, осуществляется в подписках Active Directory. Дополнительные сведения см. в статье [Управление субъектами-службами](how-to-manage-service-principals.md).

### <a name="authenticate-with-a-json-file"></a>Проверка подлинности с использованием JSON-файла

С помощью этого метода вы создадите файл JSON, содержащий необходимые учетные данные для субъекта-службы. Затем вы создадите клиентский объект пакета SDK с использованием этого файла. Этот метод можно использовать локально и в облаке. 

1. Создайте файл JSON в следующем формате.

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

    Укажите вместо четырех заполнителей идентификатор подписки Azure, идентификатор арендатора, идентификатор клиента и секрет клиента.

    > [!TIP]
    > Как указано в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development), для непосредственного создания этого формата JSON можно использовать команду [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) c параметром `--sdk-auth`.

1. Сохраните файл с именем, например *credentials.json*, в безопасном расположении, к которому код может получить доступ. Чтобы защитить учетные данные, обязательно исключите этот файл из системы управления версиями и не предоставляйте его другим разработчикам. То есть идентификатор арендатора, идентификатор клиента и секрет клиента субъекта-службы всегда должны быть изолированны на рабочей станции разработки.

1. Создайте переменную среды `AZURE_AUTH_LOCATION`, указав путь к файлу JSON в качестве значения:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_AUTH_LOCATION=../credentials.json
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    AZURE_AUTH_LOCATION="../credentials.json"
    ```

    ---

    В этих примерах предполагается, что файлу JSON присвоено имя *credentials.json* и он находится в родительской папке проекта.


1. Чтобы создать клиентский объект, используйте метод [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-):

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.resource import SubscriptionClient

    # This form of get_client_from_auth_file relies on the AZURE_AUTH_LOCATION
    # environment variable.
    subscription_client = get_client_from_auth_file(SubscriptionClient)

    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

Кроме того, вы можете указать путь непосредственно в коде, используя аргумент `auth_path`. В этом случае переменная среды не требуется.

```python
subscription_client = get_client_from_auth_file(SubscriptionClient, auth_path="../credentials.json")
```

### <a name="authenticate-with-a-json-dictionary"></a>Проверка подлинности с использованием словаря JSON

```python
import os
from azure.common.client_factory import get_client_from_json_dict
from azure.mgmt.resource import SubscriptionClient

# Retrieve the IDs and secret to use in the JSON dictionary
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

config_dict = {
   "subscriptionId": subscription_id,
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

subscription_client = get_client_from_json_dict(SubscriptionClient, config_dict)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Чтобы не использовать файл, описанный в предыдущем разделе, вы можете создать необходимые данные JSON в переменной и вызвать [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-). В этом коде предполагается, что вы создали переменные среды, описанные в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development). Для развернутого в облаке кода эти переменные среды можно создавать на виртуальной машине сервера или в качестве параметров приложения при использовании службы платформы, такой как Служба приложений Azure и Функции Azure.

Вы также можете хранить значения в Azure Key Vault и извлекать их во время выполнения, а не с помощью переменных среды.

### <a name="authenticate-with-token-credentials"></a>Аутентификация с использованием учетных данных токена

```python
import os
from azure.mgmt.resource import SubscriptionClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

credential = ServicePrincipalCredentials(tenant=tenant_id, client_id=client_id, secret=client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

В этом методе вы создаете объект [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials?view=azure-python) с использованием учетных данных, полученных из защищенного хранилища, например Azure Key Vault или переменных среды. В предыдущем коде предполагается, что вы создали переменные среды, описанные в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development).

С помощью этого метода вместо общедоступного облака Azure вы можете использовать [национальное облако Azure](/azure/active-directory/develop/authentication-national-cloud), указав для клиентского объекта аргумент `base_url`:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

Константы национального облака находятся в [библиотеке msrestazure.azure_cloud](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="authenticate-with-token-credentials-and-an-adal-context"></a>Проверка подлинности с использованием учетных данных токена и контекста ADAL

Чтобы при применении учетных данных токена обеспечить дополнительный контроль, используйте [Библиотеку проверки подлинности Azure Active Directory (ADAL) для Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) и программу-оболочку ADAL для SDK.

```python
import os, adal
from azure.mgmt.resource import SubscriptionClient
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + tenant_id)

credential = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, client_id, client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Если вам нужна библиотека ADAL, запустите `pip install adal`.

С помощью этого метода вместо общедоступного облака Azure вы можете использовать [национальное облако Azure](/azure/active-directory/develop/authentication-national-cloud).

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

# ...

LOGIN_ENDPOINT = AZURE_CHINA_CLOUD.endpoints.active_directory
RESOURCE = AZURE_CHINA_CLOUD.endpoints.active_directory_resource_id
```

Просто замените `AZURE_PUBLIC_CLOUD` соответствующей константой национального облака из [библиотеки msrestazure.azure_cloud](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="cli-based-authentication-development-purposes-only"></a>Проверка подлинности на основе CLI (только для разработки)

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import SubscriptionClient

subscription_client = get_client_from_cli_profile(SubscriptionClient)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

В этом методе вы создаете клиентский объект, используя учетные данные пользователя, вошедшего в систему с помощью команды Azure CLI `az login`.

Для пакета SDK используется идентификатор подписки по умолчанию. Либо вы можете указать подписку, используя команду [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli).

Этот метод следует использовать только на ранних этапах экспериментирования и разработки, так как вошедший в систему пользователь обычно имеет права владельца или администратора и может получать доступ к большинству ресурсов без каких-либо дополнительных разрешений. Дополнительные сведения см. в предыдущем примечании об [использовании учетных данных CLI с методом `DefaultAzureCredential`](#cli-auth-note).

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Устарело: проверка подлинности с использованием UserPassCredentials

До появления [Библиотеки проверки подлинности Azure Active Directory для Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) использовался нерекомендуемый класс [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials?view=azure-python). Этот класс не поддерживает двухфакторную проверку подлинности. Его использование не рекомендуется.

## <a name="see-also"></a>См. также раздел

- [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md)
- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка к работе и использование службы хранилища Azure](azure-sdk-example-storage.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
