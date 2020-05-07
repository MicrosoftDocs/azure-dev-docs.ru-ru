---
title: Краткое руководство. Создание виртуальной машины Linux с инфраструктурой в Azure с помощью Terraform
description: Узнайте, как использовать Terraform для создания готовой среды виртуальных машин Linux в Azure и управления ею.
keywords: Azure DevOps, Terraform, виртуальная машина Linux, виртуальная машина
ms.topic: quickstart
ms.date: 05/04/2020
ms.openlocfilehash: d6763118179251c224e0ec3ae6e05a630cae7cde
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801892"
---
# <a name="quickstart-create-a-linux-vm-with-infrastructure-in-azure-using-terraform"></a>Краткое руководство. Создание виртуальной машины Linux с инфраструктурой в Azure с помощью Terraform

Terraform позволяет определить и создать развертывания комплексной инфраструктуры в Azure. Шаблоны Terraform нужно создавать в понятном формате, чтобы их можно было использовать для создания и настройки ресурсов Azure в согласованном и воспроизводимом виде. В этой статье показано, как создать готовую среду Linux и вспомогательные ресурсы с помощью Terraform. Вы также узнаете, как [установить и настроить Terraform](install-configure.md).

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="create-azure-connection-and-resource-group"></a>Создание подключения Azure и группы ресурсов

Мы разберем каждый раздел шаблона Terraform. Здесь также приведена полная версия [шаблона Terraform](#complete-terraform-script), который можно скопировать и вставить.

В разделе `provider` указывается, что Terraform нужно использовать поставщик Azure. Значения для `subscription_id`, `client_id`, `client_secret` и `tenant_id` см. в статье об [установке и настройке Terraform](install-configure.md). 

> [!TIP]
> При создании переменных среды для значений или использовании [Azure Cloud Shell Bash](/azure/cloud-shell/overview) не нужно включать объявления переменных в этом разделе.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

В следующем разделе создается группа ресурсов с именем `myResourceGroup` в расположении `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

В дополнительных разделах нужно ссылаться на группу ресурсов, используя `azurerm_resource_group.myterraformgroup.name`.

## <a name="create-virtual-network"></a>Создание виртуальной сети

В рамках следующего раздела создается виртуальная сеть с именем `myVnet` в адресном пространстве `10.0.0.0/16`.

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

В рамках следующего раздела создается подсеть с именем `mySubnet` в виртуальной сети `myVnet`.

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к ресурсам через Интернет создайте общедоступный IP-адрес и назначьте его виртуальной машине. В рамках следующего раздела создается общедоступный IP-адрес с именем `myPublicIP`.

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="create-network-security-group"></a>Создание группы безопасности сети

Группы безопасности сети контролируют передачу исходящего и входящего сетевого трафика виртуальной машины. В рамках следующего раздела создается группа безопасности сети с именем `myNetworkSecurityGroup` и определяется правило, разрешающее передачу трафика SSH через TCP-порт 22.

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Создание виртуальных сетевых адаптеров

Виртуальный сетевой адаптер позволяет виртуальной машине подключаться к определенной виртуальной сети, общедоступному IP-адресу и группе безопасности сети. В рамках следующего раздела шаблона Terraform создается виртуальный сетевой адаптер с именем `myNIC`, подключенный к созданным ресурсам виртуальной сети:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "azurerm_subnet.myterraformsubnet.id"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "azurerm_public_ip.myterraformpublicip.id"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Создание учетной записи хранения для диагностики

Чтобы сохранить диагностику загрузки для виртуальной машины, необходима учетная запись хранения. Диагностика загрузки может помочь в устранении неполадок и наблюдении за состоянием виртуальной машины. Создаваемая учетная запись хранения используется только для хранения данных диагностики загрузки. Каждая учетная запись хранения должна иметь уникальное имя, поэтому в следующем разделе генерируется некоторый произвольный текст:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Теперь создайте учетную запись хранения. В следующем разделе создается учетная запись хранения с именем, основанным на произвольном тексте, созданном на предыдущем шаге:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Создание виртуальной машины

Последним шагом является создание виртуальной машины и использование всех созданных ресурсов. В рамках следующего раздела создается виртуальная машина с именем `myVM` и подключается виртуальный сетевой адаптер с именем `myNIC`. Используется образ последней версии `Ubuntu 16.04-LTS`, а пользователь с именем `azureuser` создается с отключенной проверкой подлинности по паролю.

 Данные ключа SSH предоставлены в разделе `ssh_keys`. Укажите открытый ключ SSH в поле `key_data`.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Полный скрипт Terraform

Чтобы объединить все эти разделы и увидеть Terraform в действии, создайте файл с именем `terraform_azure.tf` и вставьте следующее содержимое:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Создание и развертывание инфраструктуры

После создания шаблона Terraform необходимо сразу инициализировать Terraform. Это действие гарантирует, что в Terraform имеются все необходимые компоненты для создания шаблона в Azure.

```bash
terraform init
```

Далее необходима проверка шаблона в Terraform. Этот шаг сравнивает запрашиваемые ресурсы со сведениями о состоянии, сохраненными Terraform, а затем выводит запланированное выполнение. На этом этапе ресурсы Azure не создаются.

```bash
terraform plan
```

После выполнения предыдущей команды вы должны увидеть что-то вроде этого:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Если все выглядит правильно и вы готовы создать инфраструктуру в Azure, примените шаблон в Terraform:

```bash
terraform apply
```

После завершения действий в Terraform инфраструктура виртуальной машины будет готова. Получите общедоступный IP-адрес виртуальной машины с помощью команды [az vm show](/cli/azure/vm).

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

После этого можно подключиться по SSH к виртуальной машине:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Документация по Terraform в Azure](/azure/terraform)