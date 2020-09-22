---
title: Проверка подлинности приложений Python с помощью служб Azure
description: Как получить необходимые объекты учетных данных для аутентификации приложения Python с помощью служб Azure и с использованием библиотек Azure
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 746a948077c7def12aae5053355c445b7592eae0
ms.sourcegitcommit: 4824cea71195b188b4e8036746f58bf8b70dc224
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89753748"
---
# <a name="how-to-authenticate-and-authorize-python-apps-on-azure"></a>Как аутентифицировать и авторизовать приложения Python в Azure

Большинству облачных приложений, развернутых в Azure, требуется доступ к другим ресурсам Azure, таким как хранилище, базы данных, сохраненные секреты и т. д. Для доступа к этим ресурсам приложение должно пройти аутентификацию и авторизацию.

- При **аутентификации** проверяется удостоверение приложения с помощью Azure Active Directory.

- При **авторизации** определяется, какие операции может выполнять аутентифицированное приложение с тем или иным ресурсом. Разрешенные операции определяют **роли**, которые назначены удостоверению приложения для этого ресурса. В некоторых случаях (например, при использовании Azure Key Vault) авторизация также определяется дополнительными **политиками доступа**, назначенными удостоверению приложения.

В этой статье подробно описываются аутентификация и авторизация:

- Как назначить удостоверение приложения.
- Как предоставить разрешения удостоверению.
- Как и в каких случаях выполняются аутентификация и авторизация.
- Различные средства, с помощью которых приложение проходит аутентификацию в Azure, используя библиотеки Azure. Использование `DefaultAzureCredential` рекомендуется, но не является обязательным.

## <a name="how-to-assign-an-app-identity"></a>Как назначить удостоверение приложения

В Azure удостоверение приложения определяет **субъект-служба**. (Субъект-служба — это конкретный тип "субъекта безопасности", который используется для идентификации приложения или службы. Например, это может быть фрагмент кода, а не пользователь или группа пользователей.)

Задействованный субъект-служба зависит от того, где выполняется приложение, как описано в следующих разделах.

### <a name="identity-when-running-the-app-on-azure"></a>Удостоверение при запуске приложения в Azure

Если приложение запущено в облаке (например, в рабочей среде), то скорее всего оно использует **назначаемое системой управляемое удостоверение**. При использовании [управляемого удостоверения](/azure/active-directory/managed-identities-azure-resources/overview) вы указываете имя приложения, когда назначаете роли и разрешения для ресурсов. Azure автоматически управляет базовым субъектом-службой и аутентифицирует приложение для доступа к этим ресурсам Azure. В результате вам не нужно напрямую управлять субъектом-службой. Кроме того, коду приложения не требуется обрабатывать маркеры доступа, секреты или строки подключения для ресурсов Azure, что снижает риск утечки или компрометации подобной информации.

Настройка управляемого удостоверения зависит от службы, используемой для размещения вашего приложения. Ссылки на инструкции для каждой службы приведены в статье [Службы с поддержкой управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Например, для веб-приложений, развернутых в Службе приложений Azure, вы включаете его управляемое удостоверение с помощью параметра **Удостоверение** > **Назначено системой** на портале Azure или с помощью команды `az webapp identity assign` в Azure CLI.

Если вы не можете использовать управляемое удостоверение, то можно вручную зарегистрировать приложение в Azure Active Directory. При регистрации приложению назначается субъект-служба, который затем используется для назначения ролей и разрешений. Дополнительные сведения см. в статье [Регистрация приложения для внедрения содержимого Power BI](/azure/active-directory/develop/quickstart-register-app).

### <a name="identity-when-running-the-app-locally"></a>Удостоверение при запуске приложения в локальной среде

При разработке часто требуется запускать и отлаживать код приложения на рабочей станции разработчика, но при этом данный код должен иметь доступ к ресурсам Azure, размещенным в облаке. В этом случае можно создать отдельный субъект-службу с помощью Azure Active Directory, предназначенный специально для локальной разработки. Необходимо назначить этому субъекту-службе роли и разрешения для соответствующих ресурсов. Как правило, этому удостоверению разработки разрешается доступ только к ресурсам вне рабочей среды.

Дополнительные сведения о том, как создать локальный субъект-службу и предоставить его библиотекам Azure, см. в разделе [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md). После завершения этой одноразовой настройки можно будет выполнять один и тот же код приложения в локальной и облачной средах, не внося какие-либо изменения в среду.

У каждого разработчика должен быть свой субъект-служба, хранящийся в учетной записи пользователя на рабочей станции. Субъект-службу нельзя хранить в репозиториях с системой управления версиями. В случае кражи или взлома субъекта-службы его можно удалить, чтобы отозвать все его разрешения, а затем создать новый субъект-службу для этого разработчика. Дополнительные сведения см. в статье [Управление субъектами-службами](how-to-manage-service-principals.md).

> [!NOTE]
> Хотя приложение можно запускать с помощью собственных учетных данных пользователя Azure, это не поможет определить разрешения на доступ к ресурсам, необходимые приложению в случае развертывания в облаке. Гораздо удобнее настроить субъект-службу для разработки и назначить ему необходимые роли и разрешения, которые затем можно реплицировать с помощью управляемого удостоверения или субъекта-службы развернутого приложения.

## <a name="assign-roles-and-permissions-to-an-identity"></a>Назначение ролей и разрешений удостоверению

Определив удостоверения приложения для запуска в Azure и локальной среде, вы можете использовать управление доступом на основе ролей (RBAC), чтобы предоставить разрешения с помощью портала Azure или Azure CLI. Полные сведения об этом см. в разделе [Как назначить разрешения роли удостоверению приложения или субъекту-службе](how-to-assign-role-permissions.md).

## <a name="when-does-authentication-and-authorization-occur"></a>В каких случаях выполняются аутентификация и авторизация?

При написании кода приложения с использованием библиотек Azure (пакет SDK) для Python для доступа к ресурсам Azure применяется приведенный ниже шаблон.

1. Получите учетные данные, описывающие удостоверение приложения, используя один из методов, описанных далее в этой статье.

1. Используйте эти учетные данные, чтобы получить клиентский объект для соответствующего ресурса. (У каждого типа ресурса имеется собственный клиентский объект в библиотеках Azure, для которого передается URL-адрес ресурса.)

1. Попытайтесь получить доступ к ресурсу или изменить его через клиентский объект, который создает HTTP-запрос к REST API ресурса. Вызов API — это точка, в которой Azure аутентифицирует удостоверение приложения и выполняет авторизацию.

Приведенный ниже код описывает и демонстрирует эти действия, пытаясь получить доступ к Azure Key Vault.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL. In this code we assume the resource URL is in an
# environment variable, KEY_VAULT_URL in this case.

vault_url = os.environ["KEY_VAULT_URL"]


# Acquire a credential object for the app identity. When running in the cloud,
# DefaultAzureCredential uses the app's managed identity or user-assigned service principal.
# When run locally, DefaultAzureCredential relies on environment variables named
# AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.

credential = DefaultAzureCredential()


# Acquire an appropriate client object for the resource identified by the URL. The
# client object only stores the given credential at this point but does not attempt
# to authenticate it.
#
# **NOTE**: SecretClient here is only an example; the same process applies to all
# other Azure client libraries.

secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation on the resource using the client object (in
# this case, retrieve a secret from Key Vault). The operation fails for any of
# the following reasons:
#
# 1. The information in the credential object is invalid (for example, the AZURE_CLIENT_ID
#    environment variable cannot be found).
# 2. The app identity cannot be authenticated using the information in the credential object.
# 3. The app identity is not authorized to perform the requested operation on the
#    resource (identified in this case by the vault_url.

retrieved_secret = secret_client.get_secret("secret-name-01")
```

Опять же, аутентификация или авторизация не выполняется до тех пор, пока код не выполнит определенный запрос к REST API Azure через клиентский объект. Оператор для создания `DefaultAzureCredential` [см. следующий раздел] только создает объект на стороне клиента в памяти, но не выполняет другие проверки. 

Создание объекта пакета SDK [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient) также не подразумевает взаимодействия с рассматриваемым ресурсом. Объект `SecretClient` — это просто оболочка для базового REST API Azure, которая существует только в памяти среды выполнения приложения. 

Только при вызове в коде метода [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient#get-secret-name--version-none----kwargs-) клиентский объект создает соответствующий вызов REST API к Azure. Затем конечная точка Azure `get_secret` выполняет проверку подлинности удостоверения вызывающей стороны и проверяет авторизацию.

## <a name="authenticate-with-defaultazurecredential"></a>Проверка подлинности с помощью метода DefaultAzureCredential

Для большинства приложений класс [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential) из библиотеки [`azure-identity`](/python/api/azure-identity/azure.identity) обеспечивает самые простые и рекомендуемые средства аутентификации. `DefaultAzureCredential` автоматически использует управляемое удостоверение приложения в облаке и автоматически загружает локальный субъект-службу из переменных среды при локальном запуске.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL
vault_url = os.environ["KEY_VAULT_URL"]

# Aquire a credential object
credential = DefaultAzureCredential()

# Acquire a client object
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation
retrieved_secret = secret_client.get_secret("secret-name-01")
```

Приведенный выше код использует объект `DefaultAzureCredential` при доступе к Azure Key Vault, где URL-адрес Key Vault доступен в переменной среды `KEY_VAULT_URL`. Код явно реализует стандартный шаблон использования библиотеки: он получает объект учетных данных, создает соответствующий клиентский объект для ресурса Azure, а затем пытается выполнить операцию с этим ресурсом с помощью данного клиентского объекта. Опять же, аутентификация и авторизация не происходят до выполнения последнего шага.

Когда код развертывается и выполняется в Azure, метод `DefaultAzureCredential` автоматически использует назначаемое системой управляемое удостоверение, которое вы можете включить для приложения в любой службе, в которой оно размещено. Разрешения для определенных ресурсов, таких как служба хранилища Azure или Azure Key Vault, назначаются этому удостоверению с помощью портала Azure или Azure CLI. В подобных случаях это удостоверение, управляемое Azure, повышает безопасность, так как вы никогда не сталкиваетесь с явной субъектом-службой в коде.

При локальном запуске кода метод `DefaultAzureCredential` автоматически использует субъект-службу, описанную в переменных среды с именами `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` и `AZURE_CLIENT_SECRET`. Затем при вызове конечной точки API клиентский объект (безопасно) включает эти значения в заголовок HTTP-запроса. При запуске в локальной или облачной среде не требуется вносить изменения в код. Дополнительные сведения о создании субъекта-службы и настройке переменных среды см. в разделе [Настройка проверки подлинности](configure-local-development-environment.md#configure-authentication).

В обоих случаях задействованному удостоверению должны быть назначены разрешения для соответствующего ресурса. Общий процесс описан в разделе [Как назначить разрешения роли удостоверению приложения или субъекту-службе](how-to-assign-role-permissions.md). Более подробные сведения можно найти в документации по отдельным службам. Дополнительные сведения о разрешениях Key Vault (например, которые потребуются для приведенного выше кода) см. в статье [Обеспечение проверки подлинности Azure Key Vault с помощью политики управления доступом](/azure/key-vault/general/group-permissions-for-apps).

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

Во всех случаях у соответствующего субъекта-службы или пользователя должны быть необходимые разрешения для используемых ресурсов и операций.

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
    > Как указано в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development), для непосредственного создания этого формата JSON можно использовать команду [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) c параметром `--sdk-auth`.

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

1. Чтобы создать клиентский объект, используйте метод [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory#get-client-from-auth-file-client-class--auth-path-none----kwargs-):

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

Чтобы не использовать файл, описанный в предыдущем разделе, вы можете создать необходимые данные JSON в переменной и вызвать [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory#get-client-from-json-dict-client-class--config-dict----kwargs-). В этом коде предполагается, что вы создали переменные среды, описанные в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development). Для развернутого в облаке кода эти переменные среды можно создавать на виртуальной машине сервера или в качестве параметров приложения при использовании службы платформы, такой как Служба приложений Azure и Функции Azure.

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

В этом методе вы создаете объект [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials) с использованием учетных данных, полученных из защищенного хранилища, например Azure Key Vault или переменных среды. В предыдущем коде предполагается, что вы создали переменные среды, описанные в разделе [Создание субъекта-службы для разработки](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development).

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

Если вам нужна библиотека ADAL, выполните `pip install adal`.

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

В этом методе вы создаете клиентский объект, используя учетные данные пользователя, вошедшего в систему с помощью команды Azure CLI `az login`. Приложение будет иметь разрешения на выполнение всех операций, разрешенных пользователю.

Для пакета SDK используется идентификатор подписки по умолчанию. Либо вы можете указать подписку до выполнения кода с помощью команды [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli). Если вам необходимо ссылаться на разные подписки в одном скрипте, используйте методы [get_client_from_auth_file](#authenticate-with-a-json-file) или [`get_client_from_json_dict`](#authenticate-with-a-json-dictionary), которые описаны ранее в этой статье.

Функцию `get_client_from_cli_profile` следует использовать только на ранних этапах экспериментирования и разработки, так как вошедший в систему пользователь обычно имеет права владельца или администратора и может получать доступ к большинству ресурсов без каких-либо дополнительных разрешений. Дополнительные сведения см. в предыдущем примечании об [использовании учетных данных CLI с методом `DefaultAzureCredential`](#cli-auth-note).

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Устарело: проверка подлинности с использованием UserPassCredentials

До появления [Библиотеки проверки подлинности Azure Active Directory для Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) использовался нерекомендуемый класс [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials). Этот класс не поддерживает двухфакторную проверку подлинности. Его использование не рекомендуется.

## <a name="see-also"></a>См. также раздел

- [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md)
- [Как назначить разрешения роли](how-to-assign-role-permissions.md)
- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка к работе и использование службы хранилища Azure](azure-sdk-example-storage.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
