---
title: Подготовка группы ресурсов с помощью библиотек Azure для Python
description: Узнайте, как с помощью библиотеки управления ресурсами из пакета Azure SDK для Python создать группу ресурсов, используя код на Python.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 0d0ff5bf8a2417e38f9fd066b226ed26da87322b
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329682"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>Пример использования библиотек Azure для подготовки группы ресурсов

В этом примере показано, как использовать библиотеки управления пакета Azure SDK в скрипте Python для подготовки группы ресурсов. ([Эквивалентные команды Azure CLI](#for-reference-equivalent-azure-cli-commands) приведены далее в этой статье.)

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-azure-library-packages"></a>2: Установка пакетов библиотек Azure

Создайте файл *requirements.txt* со следующим содержимым.

```text
azure-mgmt-resource
azure-cli-core
```

В окне терминала или в командной строке с активированной виртуальной средой установите необходимые компоненты.

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: Написание кода для подготовки группы ресурсов

Создайте файл Python с именем *provision_rg.py* с приведенным ниже содержимым. Подробные объяснения даны в комментариях:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-ResourceGroup-rg",
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

# Optional line to delete the resource group
#resource_client.resource_groups.delete("PythonAzureExample-ResourceGroup-rg")
```

Этот код использует методы проверки подлинности на основе CLI (`get_client_from_cli_profile`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте, лучше использовать `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности с использованием библиотек управления Azure для Python](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Ссылки на справку о классах, используемых в коде

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4: Выполнение скрипта

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: Проверка группы ресурсов

Проверить, создана ли группа, можно на портале Azure или с помощью Azure CLI.

- На [портале Azure](https://portal.azure.com) перейдите в раздел **Группы ресурсов** и проверьте, есть ли там созданная группа. Если страница раздела была открыта ранее, **обновите** ее.

- В Azure CLI выполните следующую команду:

    ```azurecli
    az group show -n PythonAzureExample-ResourceGroup-rg
    ```

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

```azurecli
az group delete -n PythonAzureExample-ResourceGroup-rg
```

Если созданная в этом примере группа ресурсов вам не нужна, выполните приведенную ниже команду. Создание групп ресурсов не влечет за собой расходов по подписке. Но рекомендуется удалять неиспользуемые группы.

Для удаления группы ресурсов с помощью кода также можно использовать метод [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

С помощью следующих команд Azure CLI выполняются такие же действия по подготовке, как и в сценарии Python:

```azurecli
az group create -n PythonAzureExample-ResourceGroup-rg -l centralus
```

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
