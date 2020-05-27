---
title: Подготовка виртуальной машины с помощью пакета Azure SDK для Python
description: Узнайте, как подготовить виртуальную машину Azure с помощью Python и библиотек управления Azure SDK.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 816419d99647ed23173d6c9ff707c256b384968f
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404987"
---
# <a name="example-use-the-azure-sdk-to-provision-a-virtual-machine"></a>Пример использования пакета Azure SDK для подготовки виртуальной машины

В этом примере показано, как использовать библиотеки управления Azure SDK в скрипте Python для создания группы ресурсов, содержащей виртуальную машину Linux.

В этом примере нет клиентских библиотек, так как у виртуальных машин есть только интерфейс управления.

Все описанные в этой статье команды работают одинаково как в Bash для macOS или Linux, так и в командных оболочках для Windows, если не указано иное.

> [!NOTE]
> Подготовка виртуальной машины с помощью кода — это многоэтапный процесс, предполагающий подготовку ряда других ресурсов, необходимых виртуальной машине. Если вы выполняете код из командной строки, проще будет использовать команду [`az vm create`](/cli/azure/vm?view=azure-cli-latest#az-vm-create), которая автоматически подготавливает эти вспомогательные ресурсы, задавая значения по умолчанию всем параметрам, которые вы не указываете. Единственными обязательными аргументами являются группа ресурсов, имя виртуальной машины, имя образа и учетные данные для входа. Дополнительные сведения см. в статье [Быстрое создание виртуальной машины с помощью Azure CLI](/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm-quick-create).

## <a name="1-set-up-your-local-development-environment"></a>1: настройка локальной среды разработки;

Выполните все инструкции из статьи [Настройка локальной среды разработки Python для Azure](configure-local-development-environment.md), если вы этого еще не сделали.

Обязательно создайте субъект-службу для локальной разработки, а также создайте и активируйте виртуальную среду для этого проекта.

## <a name="2-install-the-needed-sdk-libraries"></a>2: Установка необходимых библиотек SDK

1. Создайте файл *requirements.txt*, в котором перечислены библиотеки управления, используемые в этом примере.

    ```txt
    azure-mgmt-resource
    azure-mgmt-network
    azure-mgmt-compute
    azure-core-cli
    ```

1. В окне терминала или в командной строке с активированной виртуальной средой установите библиотеки управления, указанные в файле *requirements.txt*.

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-a-virtual-machine"></a>3: Написание кода для подготовки виртуальной машины

Создайте файл Python с именем *provision_vm.py* с приведенным ниже кодом. Подробные объяснения даны в комментариях:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute import ComputeManagementClient

print(f"Provisioning a virtual machine...some operations might take a minute or two.")

# Step 1: Provision a resource group

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonSDKExample-VM-rg"
LOCATION = "centralus"

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    {
        "location": LOCATION
    }
)


print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: provision a virtual network

# A virtual machine requires a network interface client (NIC). A NIC requires
# a virtual network and subnet along with an IP address. Therefore we must provision
# these downstream components first, then provision the NIC, after which we
# can provision the VM.

# Network and IP address names
VNET_NAME = "python-example-vnet"
SUBNET_NAME = "python-example-subnet"
IP_NAME = "python-example-ip"
IP_CONFIG_NAME = "python-example-ip-config"
NIC_NAME = "python-example-nic"

# Obtain the management object for networks
network_client = get_client_from_cli_profile(NetworkManagementClient)

# Provision the virtual network and wait for completion
poller = network_client.virtual_networks.create_or_update(RESOURCE_GROUP_NAME,
    VNET_NAME,
    {
        "location": LOCATION,
        "address_space": {
            "address_prefixes": ["10.0.0.0/16"]
        }
    }
)

vnet_result = poller.result()

print(f"Provisioned virtual network {vnet_result.name} with address prefixes {vnet_result.address_space.address_prefixes}")

# Step 3: Provision the subnet and wait for completion
poller = network_client.subnets.create_or_update(RESOURCE_GROUP_NAME, 
    VNET_NAME, SUBNET_NAME,
    { "address_prefix": "10.0.0.0/24" }
)
subnet_result = poller.result()

print(f"Provisioned virtual subnet {subnet_result.name} with address prefix {subnet_result.address_prefix}")

# Step 4: Provision an IP address and wait for completion
poller = network_client.public_ip_addresses.create_or_update(RESOURCE_GROUP_NAME,
    IP_NAME,
    {
        "location": LOCATION,
        "sku": { "name": "Standard" },
        "public_ip_allocation_method": "Static",
        "public_ip_address_version" : "IPV4"
    }
)

ip_address_result = poller.result()

print(f"Provisioned public IP address {ip_address_result.name} with address {ip_address_result.ip_address}")

# Step 5: Provision the network interface client
poller = network_client.network_interfaces.create_or_update(RESOURCE_GROUP_NAME,
    NIC_NAME, 
    {
        "location": LOCATION,
        "ip_configurations": [ {
            "name": IP_CONFIG_NAME,
            "subnet": { "id": subnet_result.id },
            "public_ip_address": {"id": ip_address_result.id }
        }]
    }
)

nic_result = poller.result()

print(f"Provisioned network interface client {nic_result.name}")

# Step 6: Provision the virtual machine

# Obtain the management object for virtual machines
compute_client = get_client_from_cli_profile(ComputeManagementClient)

VM_NAME = "ExampleVM"
USERNAME = "azureuser"
PASSWORD = "ChangePa$$w0rd24"

print(f"Provisioning virtual machine {VM_NAME}; this operation might take a few minutes.")

# Provision the VM specifying only minimal arguments, which defaults to an Ubuntu 18.04 VM
# on a Standard DS1 v2 plan with a public IP address and a default virtual network/subnet.

poller = compute_client.virtual_machines.create_or_update(RESOURCE_GROUP_NAME, VM_NAME,
    {
        "location": LOCATION,
        "storage_profile": {
            "image_reference": {
                "publisher": 'Canonical',
                "offer": "UbuntuServer",
                "sku": "16.04.0-LTS",
                "version": "latest"
            }
        },
        "hardware_profile": {
            "vm_size": "Standard_DS1_v2"
        },
        "os_profile": {
            "computer_name": VM_NAME,
            "admin_username": USERNAME,
            "admin_password": PASSWORD
        },
        "network_profile": {
            "network_interfaces": [{
                "id": nic_result.id,
            }]
        }
    }
)

vm_result = poller.result()

print(f"Provisioned virtual machine {vm_result.name}")
```

Этот код использует методы проверки подлинности на основе CLI (`get_client_from_cli_profile`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте (например, для автоматизации управления виртуальной машиной), используйте `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности приложений Python с помощью служб Azure](azure-sdk-authenticate.md).

## <a name="4-run-the-script"></a>4. Выполнение скрипта

```bash
python provision_vm.py
```

Процесс подготовки занимает несколько минут.

## <a name="5-verify-the-resources"></a>5. Проверка ресурсов

Откройте [портал Azure](https://portal.azure.com), перейдите к группе ресурсов PythonSDKExample-VM-rg и запишите следующие данные: виртуальная машина, виртуальный диск, группа безопасности сети, общедоступный IP-адрес, сетевой интерфейс и виртуальная сеть.

![Страница новой группы ресурсов на портале Azure, где отображаются данные о виртуальной машине и связанных ресурсах](media/azure-sdk-example-virtual-machines/portal-vm-resources.png)

### <a name="for-reference-equivalent-azure-cli-commands"></a>Для справки: аналогичные команды Azure CLI

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet \
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet \
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip \
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet \
    --subnet python-example-subnet -n python-example-nic \
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" \
    --nics python-example-nic --image UbuntuLTS \
    --admin-username azureuser --admin-password ChangePa$$w0rd24

```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet ^
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet ^
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip ^
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet ^
    --subnet python-example-subnet -n python-example-nic ^
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" ^
    --nics python-example-nic --image UbuntuLTS ^
    --admin-username azureuser --admin-password ChangePa$$w0rd24
```

---

## <a name="6-clean-up-resources"></a>6\. Очистка ресурсов

```azurecli
az group delete -n PythonSDKExample-VM-rg
```

Если вам ненужны ресурсы, созданные при работе с этим примером, выполните следующую команду, чтобы не нести расходы по подписке.

## <a name="see-also"></a>См. также раздел

Более подробные примеры использования Python для создания виртуальной машины см. в следующих ресурсах:

- [Создание виртуальной машины Azure и управление ею с помощью Python](/azure/virtual-machines/windows/python). В статье показано, как создать виртуальные машины Linux, изменив параметр `storage_profile`.
- [Примеры кода для управления виртуальными машинами Azure — Python](https://github.com/Azure-Samples/virtual-machines-python-manage) (GitHub). В этом примере демонстрируются дополнительные операции управления, такие как запуск и перезапуск виртуальной машины, ее остановка и удаление, увеличение размера диска и управление дисками данных.
