---
title: Подготовка группы ресурсов с помощью библиотек Azure для Python
description: Узнайте, как с помощью библиотеки управления ресурсами из пакета Azure SDK для Python создать группу ресурсов, используя код на Python.
ms.date: 11/12/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 65c78e480336f689096ccbd9f75420febf732f20
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872845"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>Пример использования библиотек Azure для подготовки группы ресурсов

В этом примере показано, как использовать библиотеки управления пакета Azure SDK в скрипте Python для подготовки группы ресурсов. ([Эквивалентная команда Azure CLI](#for-reference-equivalent-azure-cli-commands) приведена далее в этой статье. Если вы предпочитаете использовать портал Azure, см. раздел [Создание групп ресурсов](/azure/azure-resource-manager/management/manage-resource-groups-portal).)

Все описанные в этой статье команды работают одинаково как в Bash для Linux или macOS, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-azure-library-packages"></a>2: Установка пакетов библиотек Azure

Создайте файл *requirements.txt* со следующим содержимым.

```text
azure-mgmt-resource
azure-identity
```

В окне терминала или в командной строке с активированной виртуальной средой установите необходимые компоненты.

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: Написание кода для подготовки группы ресурсов

Создайте файл Python с именем *provision_rg.py* с приведенным ниже содержимым. Подробные объяснения даны в комментариях:

```python
# Import the needed credential and management objects from the libraries.
from azure.mgmt.resource import ResourceManagementClient
from azure.identity import AzureCliCredential
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-rg",
    {
        "location": "centralus"
    }
)

# Within the ResourceManagementClient is an object named resource_groups,
# which is of class ResourceGroupsOperations, which contains methods like
# create_or_update.
#
# The second parameter to create_or_update here is technically a ResourceGroup
# object. You can create the object directly using ResourceGroup(location=LOCATION)
# or you can express the object as inline JSON as shown here. For details,
# see Inline JSON pattern for object arguments at
# https://docs.microsoft.com/azure/developer/python/azure-sdk-overview#inline-json-pattern-for-object-arguments.

print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# The return value is another ResourceGroup object with all the details of the
# new group. In this case the call is synchronous: the resource group has been
# provisioned by the time the call returns.

# Update the resource group with tags
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-rg",
    {
        "location": "centralus",
        "tags": { "environment":"test", "department":"tech" }
    }
)

print(f"Updated resource group {rg_result.name} with tags")

# Optional lines to delete the resource group. begin_delete is asynchronous.
# poller = resource_client.resource_groups.begin_delete(rg_result.name)
# result = poller.result()
```

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [AzureCliCredential (azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)

## <a name="4-run-the-script"></a>4: Выполнение скрипта

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: Проверка группы ресурсов

Проверить, создана ли группа, можно на портале Azure или с помощью Azure CLI.

- На [портале Azure](https://portal.azure.com) перейдите в раздел **Группы ресурсов** и проверьте, есть ли там созданная группа. Если страница раздела была открыта ранее, **обновите** ее.

- В Azure CLI выполните следующую команду:

    ```azurecli
    az group show -n PythonAzureExample-rg
    ```

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

```azurecli
az group delete -n PythonAzureExample-rg  --no-wait
```

Если созданная в этом примере группа ресурсов вам не нужна, выполните приведенную ниже команду. Создание групп ресурсов не влечет за собой расходов по подписке. Но рекомендуется удалять неиспользуемые группы. Аргумент `--no-wait` позволяет команде выполнять возврат без задержки, не ожидая завершения операции.

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в сценарии Python:

```azurecli
az group create -n PythonAzureExample-rg -l centralus
```

## <a name="see-also"></a>См. также раздел

- [Пример. Перечисление групп ресурсов в подписке](azure-sdk-example-list-resource-groups.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка базы данных к работе и выполнение запросов к ней](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
