---
title: Схемы использования библиотек Azure для Python
description: Общие сведения об использовании библиотек Azure SDK для Python
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: f712dc41233b8301e370c9eb63786d8e2d7f8c70
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256279"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Библиотеки Azure для схем использования Python

Пакет Azure SDK для Python состоит из множества независимых библиотек, перечисленных на странице индексов [пакета SDK для Azure для Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

У всех библиотек есть определенные общие характеристики и схемы использования, такие как установка и использование встроенного кода JSON для аргументов объекта.

## <a name="library-installation"></a>Установка библиотеки

Чтобы установить нужный пакет библиотеки, просто используйте `pip install`:

```cmd
# Install the management library for Azure Storage
pip install azure-mgmt-storage
```

```cmd
# Install the client library for Azure Storage
pip install azure-storage-blob
```

`pip install` получает последнюю версию библиотеки в текущей среде Python.

Можно также использовать команду `pip`, чтобы удалить библиотеку или установить определенную версию, включая предварительные версии. Дополнительные сведения см. в статье [Описание установки пакетов библиотеки Azure для Python](azure-sdk-install.md).

## <a name="inline-json-pattern-for-object-arguments"></a>Встроенная структура данных JSON для аргументов объектов

Библиотеки Azure позволяют выражать аргументы объектов в виде отдельных объектов или как встроенную структуру данных JSON.

Предположим, что существует объект [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient?view=azure-python), который создает группу ресурсов с помощью метода [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-). Второй аргумент для этого метода имеет тип [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup?view=azure-python).

Для вызова `create_or_update` можно создать отдельный экземпляр `ResourceGroup`, непосредственно указав его обязательные аргументы (в нашем случае `location`):

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    ResourceGroup(location="centralus")
)
```

Второй способ — это передать эти параметры в виде встроенной структуры данных JSON:

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    {
      "location": "centralus"
    }
)
```

При использовании JSON библиотеки Azure автоматически преобразуют встроенную структуру данных JSON в соответствующий тип объекта для нужного аргумента.

Объекты также могут иметь вложенные аргументы объектов. В этом случае можно также использовать встроенную структуру данных JSON.

Предположим, что у вас есть экземпляр объекта [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) и вы вызываете его метод [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-). В этом случае третий аргумент имеет тип [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), который содержит аргумент типа [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python). В свою очередь `VaultProperties` содержит аргументы объекта типа [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) и [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). `Sku` содержит объект [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python), а каждый `AccessPolicyEntry` содержит объект [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python).

Для вызова `create_or_update` с внедренными объектами используется код, подобный приведенному ниже (при условии, что `tenant_id` и `object_id` уже определены). Кроме того, необходимые объекты можно создать перед вызовом функции.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    VaultCreateOrUpdateParameters(
        location="centralus",
        properties=VaultProperties(
            tenant_id=tenant_id,
            sku=Sku(name="standard"),
            access_policies=[
                AccessPolicyEntry(
                    tenant_id=tenant_id,
                    object_id=object_id,
                    permissions=Permissions(keys=['all'], secrets=['all'])
                )
            ]
        )
    )
)
```

Этот же вызов с помощью встроенной структуры данных JSON выглядит следующим образом:

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    {
        'location': 'centralus',
        'properties': {
            'sku': {
                'name': 'standard'
            },
            'tenant_id': tenant_id,
            'access_policies': [{
                'object_id': object_id,
                'tenant_id': tenant_id,
                'permissions': {
                    'keys': ['all'],
                    'secrets': ['all']
                }
            }]
        }
    }
)
```

Поскольку оба способа дают одинаковый результат, можно выбрать любой из них или даже совместить их.

Если структура данных JSON задана неправильно, обычно возникает такая ошибка: "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'" (Ошибка десериализации: не удалось выполнить десериализацию в объект type. Ошибка атрибута: объект str не имеет атрибута get). Как правило, причина этой ошибки заключается в том, что вы предоставляете одну строку для свойства, тогда как библиотека ожидает вложенный объект JSON. Например, использование `"sku": "standard"` в предыдущем примере приводит к возникновению этой ошибки, так как параметр `sku` является объектом `Sku`, который ожидает встроенный объект JSON, в нашем случае `{ "name": "standard"}`, соответствующий соответствует ожидаемому типу `SkuName`.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знакомы с популярными схемами использования библиотек Azure для Python, ознакомьтесь со следующими автономными примерами, чтобы изучить конкретные сценарии для управления и клиентских библиотек.

- [Пример. Создание группы ресурсов](azure-sdk-example-resource-group.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)

Эти примеры можно использовать в любом порядке, так как они не связаны между собой.
