---
title: Использование управляемых дисков Azure с помощью библиотек Azure для Python
description: Сведения о том, как с помощью пакета средств разработки Azure создавать управляемые диски, изменять их размер и обновлять их.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python
ms.openlocfilehash: b8d45f3d4b5ccd2c8a1c2850d496b9f68625ef46
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759322"
---
# <a name="use-azure-managed-disks-with-the-azure-libraries-sdk-for-python"></a>Использование управляемых дисков Azure в сочетании с библиотеками (SDK) Azure для Python

Управляемые диски Azure упрощают управление дисками, а также повышают уровень масштабируемости и безопасности без необходимости взаимодействовать с учетными записями хранения.

Для администрирования управляемых дисков используется библиотека [`azure-mgmt-compute`](/python/api/overview/azure/virtualmachines). (Пример для подготовки виртуальной машины к работе с помощью библиотеки `azure-mgmt-compute` можно изучить [в этой статье](azure-sdk-example-virtual-machines.md).)

## <a name="standalone-managed-disks"></a>Автономные управляемые диски

Вы можете создавать автономные управляемые диски несколькими способами, которые описаны в следующих разделах.

### <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'disk_size_gb': 20,
        'creation_data': {
            'create_option': DiskCreateOption.empty
        }
    }
)
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-from-blob-storage"></a>Создание управляемого диска из хранилища BLOB-объектов

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.import_enum,
            'source_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd'
        }
    }
)
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-image-from-blob-storage"></a>Создание образа управляемого диска из хранилища BLOB-объектов

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.images.begin_create_or_update(
    'my_resource_group',
    'my_image_name',
    {
        'location': 'eastus',
        'storage_profile': {
           'os_disk': {
              'os_type': 'Linux',
              'os_state': "Generalized",
              'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
              'caching': "ReadWrite",
           }
        }
    }
)
image_resource = poller.result()
```

### <a name="create-a-managed-disk-from-your-own-image"></a>Создание управляемого диска из своего образа

```python
from azure.mgmt.compute.models import DiskCreateOption

# If you don't know the id, do a 'get' like this to obtain it
managed_disk = compute_client.disks.get(self.group_name, 'myImageDisk')

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.copy,
            'source_resource_id': managed_disk.id
        }
    }
)

disk_resource = poller.result()
```

## <a name="virtual-machine-with-managed-disks"></a>Виртуальная машина с управляемыми дисками

Вы можете создать виртуальную машину с неявным выбором управляемого диска для конкретного образа диска. Это позволяет обойтись без настройки подробных параметров.

Управляемый диск создается неявно при создании виртуальной машины в Azure из образа ОС. Для параметра `storage_profile` не обязательно указывать `os_disk`, и создание учетной записи хранения не является обязательным требованием для создания виртуальной машины.

```python
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        publisher='Canonical',
        offer='UbuntuServer',
        sku='16.04-LTS',
        version='latest'
    )
)
```

Полный пример создания виртуальной машины с помощью библиотек управления Azure для Python см. в [этой статье](azure-sdk-example-virtual-machines.md).

Можно также создать `storage_profile` с помощью собственного образа.

```python
# If you don't know the id, do a 'get' like this to obtain it
image = compute_client.images.get(self.group_name, 'myImageDisk')

storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        id = image.id
    )
)
```

Вы можете легко подключить ранее подготовленный управляемый диск.

```python
vm = compute.virtual_machines.get(
    'my_resource_group',
    'my_vm'
)
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')

vm.storage_profile.data_disks.append({
    'lun': 12, # You choose the value, depending of what is available for you
    'name': managed_disk.name,
    'create_option': DiskCreateOptionTypes.attach,
    'managed_disk': {
        'id': managed_disk.id
    }
})

async_update = compute_client.virtual_machines.begin_create_or_update(
    'my_resource_group',
    vm.name,
    vm,
)
async_update.wait()
```

## <a name="virtual-machine-scale-sets-with-managed-disks"></a>Масштабируемые наборы виртуальных машин с Управляемыми дисками

До выпуска службы "Управляемые диски" вам нужно было вручную создавать учетную запись хранения для всех необходимых виртуальных машин в масштабируемом наборе, а затем в REST API этого набора указывать параметр списка `vhd_containers`, чтобы предоставить всем виртуальным машинам имя учетной записи хранения. (Инструкции по миграции см. в статье [о преобразовании шаблона масштабируемого набора в шаблон с управляемым диском](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md).)

Так как вам не нужно управлять учетными записями хранения для управляемых дисков Azure, можно использовать в точности тот же `storage_profile`, что и для создания виртуальной машины:

```python
'storage_profile': {
    'image_reference': {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
    }
},
```

Полный пример выглядит так:

```python
naming_infix = "PyTestInfix"

vmss_parameters = {
    'location': self.region,
    "overprovision": True,
    "upgrade_policy": {
        "mode": "Manual"
    },
    'sku': {
        'name': 'Standard_A1',
        'tier': 'Standard',
        'capacity': 5
    },
    'virtual_machine_profile': {
        'storage_profile': {
            'image_reference': {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "16.04-LTS",
                "version": "latest"
            }
        },
        'os_profile': {
            'computer_name_prefix': naming_infix,
            'admin_username': 'Foo12',
            'admin_password': 'BaR@123!!!!',
        },
        'network_profile': {
            'network_interface_configurations' : [{
                'name': naming_infix + 'nic',
                "primary": True,
                'ip_configurations': [{
                    'name': naming_infix + 'ipconfig',
                    'subnet': {
                        'id': subnet.id
                    }
                }]
            }]
        }
    }
}

# Create VMSS test
result_create = compute_client.virtual_machine_scale_sets.begin_create_or_update(
    'my_resource_group',
    'my_scale_set',
    vmss_parameters,
)
vmss_result = result_create.result()
```

## <a name="other-operations-with-managed-disks"></a>Другие операции с управляемыми дисками

### <a name="resizing-a-managed-disk"></a>Изменение размера управляемого диска

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.disk_size_gb = 25

async_update = self.compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="update-the-storage-account-type-of-the-managed-disks"></a>Обновление типа учетной записи хранения управляемых дисков

```python
from azure.mgmt.compute.models import StorageAccountTypes

managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.account_type = StorageAccountTypes.standard_lrs

async_update = self.compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="create-an-image-from-blob-storage"></a>Создание образа из хранилища BLOB-объектов

```python
async_create_image = compute_client.images.create_or_update(
    'my_resource_group',
    'myImage',
    {
        'location': 'westus',
        'storage_profile': {
            'os_disk': {
                'os_type': 'Linux',
                'os_state': "Generalized",
                'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
                'caching': "ReadWrite",
            }
        }
    }
)
image = async_create_image.result()
```

### <a name="create-a-snapshot-of-a-managed-disk-that-is-currently-attached-to-a-virtual-machine"></a>Создание моментального снимка управляемого диска, который сейчас подключен к виртуальной машине

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')

async_snapshot_creation = self.compute_client.snapshots.begin_create_or_update(
        'my_resource_group',
        'mySnapshot',
        {
            'location': 'westus',
            'creation_data': {
                'create_option': 'Copy',
                'source_uri': managed_disk.id
            }
        }
    )
snapshot = async_snapshot_creation.result()
```

## <a name="see-also"></a>См. также раздел

- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)
- [Пример. Подготовка группы ресурсов к работе](azure-sdk-example-resource-group.md)
- [Пример. Перечисление групп ресурсов в подписке](azure-sdk-example-list-resource-groups.md)
- [Пример. Подготовка службы хранилища Azure к работе](azure-sdk-example-storage.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage-use.md)
- [Пример. Подготовка к работе и использование базы данных MySQL](azure-sdk-example-database.md)
- [Пройдите короткий опрос об Azure SDK для Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)
