---
title: Подготовка и развертывание веб-приложения с помощью библиотек Azure SDK
description: Узнайте, как с помощью библиотек управления из библиотек Azure SDK для Python подготовить веб-приложение, а затем развернуть код приложения из репозитория GitHub.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 8196e86b4a4311b48b47975fd47bb04f11a1fe23
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329642"
---
# <a name="example-use-the-azure-libraries-to-provision-and-deploy-a-web-app"></a>Пример использования библиотек Azure для подготовки и развертывания веб-приложения

В этом примере показано, как использовать библиотеки управления пакета Azure SDK в скрипте Python для подготовки веб-приложения в службе приложений Azure и развернуть код приложения из репозитория GitHub. ([Эквивалентные команды Azure CLI](#for-reference-equivalent-azure-cli-commands) приведены далее в этой статье.)

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Установка необходимых пакетов библиотеки Azure

Создайте файл *requirements.txt* со следующим содержимым.

```text
azure-mgmt-resource
azure-mgmt-web
azure-cli-core
```

В окне терминала или в командной строке с активированной виртуальной средой установите необходимые компоненты.

```cmd
pip install -r requirements.txt
```

## <a name="3-fork-the-sample-repository"></a>3: Создание вилки репозитория с примером

Перейдите по адресу [https://github.com/Azure-Samples/python-docs-hello-world](https://github.com/Azure-Samples/python-docs-hello-world) и создайте вилку репозитория в своей учетной записи GitHub. Вилка позволяет убедиться, что у вас есть разрешения на развертывание репозитория в Azure.

![Создание вилки репозитория GitHub с примером](media/azure-sdk-example-web-app/fork-github-repository.png)

Создайте переменную среды с именем `REPO_URL`. Укажите в ней URL-адрес вилки. Эта переменная среды будет в примере кода, показанном в следующем разделе.

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set REPO_URL=<url_of_your_fork>
```

# <a name="bash"></a>[bash](#tab/bash)

```bash
REPO_URL=<url_of_your_fork>
```

---

## <a name="4-write-code-to-provision-and-deploy-a-web-app"></a>4: Написание кода для подготовки и развертывания веб-приложения

Создайте файл Python с именем *provision_deploy_webapp.py* с приведенным ниже содержимым. Подробные объяснения даны в комментариях:

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.web import WebSiteManagementClient

# Retrieve subscription ID from environment variable
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-WebApp-rg'
LOCATION = "centralus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


#Step 2: Provision the App Service plan, which defines the underlying VM for the web app.

# Names for the App Service plan and App Service. We use a random number with the
# latter to create a reasonably unique name. If you've already provisioned a
# web app and need to re-run the script, set the WEB_APP_NAME environment 
# variable to that name instead.
SERVICE_PLAN_NAME = 'PythonAzureExample-WebApp-plan'
WEB_APP_NAME = os.environ.get("WEB_APP_NAME", f"PythonAzureExample-WebApp-{random.randint(1,100000):05}")

# Obtain the client object
app_service_client = get_client_from_cli_profile(WebSiteManagementClient)

# Provision the plan; Linux is the default
poller = app_service_client.app_service_plans.create_or_update(RESOURCE_GROUP_NAME,
    SERVICE_PLAN_NAME,
    {
        "location": LOCATION,
        "reserved": True,
        "sku" : {"name" : "B1"}
    }
)

plan_result = poller.result()

print(f"Provisioned App Service plan {plan_result.name}")


# Step 3: With the plan in place, provision the web app itself, which is the process that can host
# whatever code we want to deploy to it.

poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()

print(f"Provisioned web app {web_app_result.name} at {web_app_result.default_host_name}")

# Step 4: deploy code from a GitHub repository. For Python code, App Service on Linux runs
# the code inside a container that makes certain assumptions about the structure of the code.
# For more information, see How to configure Python apps,
# https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python.
#
# The create_or_update_source_control method doesn't provision a web app. It only sets the
# source control configuration for the app. In this case we're simply pointing to
# a GitHub repository.
#
# You can call this method again to change the repo.

REPO_URL = 'https://github.com/kraigb/python-docs-hello-world'

poller = app_service_client.web_apps.create_or_update_source_control(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": "GitHub",
        "repo_url": REPO_URL,
        "branch": "master"
    }
)

sc_result = poller.result()

print(f"Set source control on web app to {sc_result.branch} branch of {sc_result.repo_url}")
```

Этот код использует методы проверки подлинности на основе CLI (`get_client_from_cli_profile`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте, лучше использовать `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности с использованием библиотек управления Azure для Python](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [WebSiteManagementClient (azure.mgmt.web import)](/python/api/azure-mgmt-web/azure.mgmt.web.websitemanagementclient?view=azure-python)

## <a name="5-run-the-script"></a>5: Выполнение скрипта

```cmd
python provision_deploy_web_app.py
```

## <a name="6-verify-the-web-app-deployment"></a>6\. Проверка развертывания веб-приложения

1. Перейдите на сайт развертывания, выполнив следующую команду:

    ```azurecli
    az webapp browse -n PythonAzureExample-WebApp-12345
    ```

    Замените PythonAzureExample-WebApp-12345 именем своего веб-приложения.

    Вы должны увидеть текст "Hello World!" в браузере.

1. На [портале Azure](https://portal.azure.com) перейдите в раздел **Группы ресурсов** и проверьте, есть ли там группа ресурсов PythonAzureExample-WebApp-rg. Откройте ее и проверьте, присутствуют ли там требуемые ресурсы (план и экземпляр Службы приложений).

## <a name="7-clean-up-resources"></a>7: Очистка ресурсов

```azurecli
az group delete -n PythonAzureExample-WebApp-rg
```

Если вам не нужны ресурсы, созданные при работе с этим примером, выполните приведенную ниже команду, чтобы не нести расходы по подписке.

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в сценарии Python:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 ^
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

# You can use --deployment-source-url with the first create command. It's shown here
# to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan ^
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

# Replace <your_fork> with the specific URL of your forked repository.
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 \
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

# You can use --deployment-source-url with the first create command. It's shown here
# to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan \
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

# Replace <your_fork> with the specific URL of your forked repository.
```

---

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
