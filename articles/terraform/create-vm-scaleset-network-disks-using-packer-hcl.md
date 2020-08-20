---
title: созданию масштабируемого набора виртуальных машин Azure из пользовательского образа Packer с помощью Terraform
description: Узнайте, как использовать Terraform для настройки и управления версиями масштабируемого набора виртуальных машин Azure из пользовательского образа, созданного с помощью Packer
ms.topic: how-to
ms.date: 11/07/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: de8f22311edefcef1f25571731f17e5e5bc96348
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241196"
---
# <a name="create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>созданию масштабируемого набора виртуальных машин Azure из пользовательского образа Packer с помощью Terraform

[Масштабируемые наборы виртуальных машин Azure](/azure/virtual-machine-scale-sets) позволяют настроить идентичные виртуальные машины. Количество экземпляров виртуальных машин можно настроить по запросу или расписанию. Дополнительные сведения см. в статье [Автоматическое масштабирование масштабируемого набора виртуальных машин на портале Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка развертывания Terraform;
> * использование переменных и выходных данных для развертывания Terraform;
> * создание и развертывание сетевой инфраструктуры;
> * создание пользовательского образа виртуальной машины с помощью Packer;
> * создание и развертывание масштабируемого набора виртуальных машин с помощью пользовательского образа;
> * создание и развертывание jumpbox.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Terraform**. [Установка Terraform и настройка доступа к Azure](get-started-cloud-shell.md).
- **Пара ключей SSH**: [Создание пары ключей SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Packer**:  [Установка Packer](https://learn.hashicorp.com/packer/getting-started/install).

## <a name="create-the-file-structure"></a>Создание структуры файлов

В пустом каталоге создайте три файла со следующими именами:

- `variables.tf`: этот файл содержит значения переменных, используемых в шаблоне.
- `output.tf`: этот файл описывает параметры, отображающиеся после развертывания.
- `vmss.tf`: этот файл содержит код инфраструктуры, для которой выполняется развертывание.

##  <a name="create-the-variables"></a>Создание переменных 

На этом шаге можно задать переменные, которые настраивают ресурсы, созданные Terraform.

Измените файл `variables.tf`, скопировав следующий код, а затем сохранив изменения.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> По умолчанию значение переменной resource_group_name не задано. Определите собственное значение.

Сохраните файл.

При развертывании шаблона Terraform нужно получить полное доменное имя, используемое для получения доступа к приложению. Используйте тип ресурса Terraform `output` и получите свойство ресурса `fqdn`. 

Измените файл `output.tf` и скопируйте следующий код для предоставления полного доменного имени для виртуальных машин. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Определение сетевой инфраструктуры в шаблоне 

На этом шаге в новой группе ресурсов Azure создается указанная ниже сетевая инфраструктура: 
  - Одна виртуальная сеть с диапазоном адресов 10.0.0.0/16.
  - Одна подсеть с пространством адресов 10.0.2.0/24.
  - Два общедоступных IP-адреса. Один используется подсистемой балансировки нагрузки масштабируемого набора виртуальных машин. Второй — для подключения к jumpbox SSH.

Понадобится также группа, в которой создаются все ресурсы. 

Измените и скопируйте следующий код в файл `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags = {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags = {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "Static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags = {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Пометьте ресурсы, развертываемые в Azure, чтобы упростить их идентификацию в будущем.

## <a name="create-the-network-infrastructure"></a>Создание сетевой инфраструктуры

Инициализируйте среду Terraform, выполнив следующую команду в каталоге, где был создан файл `.tf`:

```bash
terraform init 
```
 
Модули поставщика скачиваются из реестра Terraform в папку `.terraform` в каталоге, в котором выполняется команда.

Выполните следующую команду, чтобы развернуть инфраструктуру в Azure.

```bash
terraform apply
```

Убедитесь, что полное доменное имя общедоступного IP-адреса соответствует конфигурации.

![Полное доменное имя масштабируемого набора виртуальных машин Terraform для общедоступного IP-адреса](./media/create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

В группе ресурсов содержатся следующие ресурсы:

![Сетевые ресурсы Terraform масштабируемого набора виртуальных машин](./media/create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Создание образа Azure с помощью Packer
Создайте пользовательский образ Linux, выполнив действия, описанные в статье [Создание образов виртуальных машин Linux в Azure с помощью Packer](/azure/virtual-machines/linux/build-image-with-packer).
 
Выполните шаги из статьи, чтобы создать отозванный образ Ubuntu с установленным Nginx.

![После создания вы получаете образ Packer](./media/create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> В рамках этой статьи установка Nginx в образе Packer выполняется с помощью команды. Во время создания можно также запустить собственный сценарий.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Изменение инфраструктуры для добавления масштабируемого набора виртуальных машин

На этом шаге создайте следующие ресурсы в предварительно развернутой сети.
- Подсистему балансировки нагрузки Azure для обслуживания приложения. Присоедините ее к общедоступному IP-адресу, который был развернут ранее.
- Одна подсистема балансировки нагрузки Azure и правила для обслуживания приложения. Присоедините ее к общедоступному IP-адресу, который был настроен ранее.
- Серверный пул адресов. Назначьте его подсистеме балансировки нагрузки.
- Порт проверки работоспособности, используемый приложением и настраиваемый в подсистеме балансировки нагрузки.
- Масштабируемый набор виртуальных машин, расположенный за подсистемой балансировки нагрузки и работающий в виртуальной сети, которая была развернута ранее.
- [Nginx](https://nginx.org/) на узлах масштабирования виртуальных машин, установленных из пользовательского образа.


Добавьте следующий код в конец файла `vmss.tf`.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags = {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags = {
    environment = "codelab"
  }
}

```

Настройте развертывание, добавив следующий код в `variables.tf`:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Развертывание масштабируемого набора виртуальных машин в Azure

Выполните следующую команду, чтобы визуализировать развертывание масштабируемого набора виртуальных машин:

```bash
terraform plan
```

Выходные данные команды должны выглядеть следующим образом:

![Добавление плана масштабируемого набора виртуальных машин в Terraform](./media/create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Разверните дополнительные ресурсы в Azure: 

```bash
terraform apply 
```

Содержимое группы ресурсов выглядит примерно следующим образом:

![Группа ресурсов масштабируемого набора виртуальных машин Terraform](./media/create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Откройте браузер и подключитесь к полному доменному имени, которое было возвращено командой. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Добавление jumpbox в имеющуюся сеть 

Этот необязательный шаг позволяет получить доступ к экземплярам масштабируемого набора виртуальных машин по SSH с помощью jumpbox.

Добавьте следующие ресурсы в имеющееся развертывание:
- Сетевой интерфейс подключен к той же подсети, что и масштабируемый набор виртуальных машин
- Виртуальная машина с сетевым интерфейсом.

Добавьте следующий код в конец файла `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "Static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags = {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags = {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags = {
    environment = "codelab"
  }
}
```

Измените `outputs.tf`, чтобы добавить следующий код, отображающий имя узла jumpbox после развертывания:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Развертывание jumpbox

Разверните jumpbox.

```bash
terraform apply 
```

После развертывания содержимое группы ресурсов выглядит как на картинке ниже:

![Группа ресурсов масштабируемого набора виртуальных машин Terraform](./media/create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Вход с паролем отключен в jumpbox и развернутом масштабируемом наборе виртуальных машин. Войдите с помощью SSH, чтобы получить доступ к виртуальным машинам.

## <a name="clean-up-the-environment"></a>Очистка среды

С помощью следующих команд удаляются ресурсы, созданные при работе с этой статьей:

```bash
terraform destroy
```

Введите *yes* для подтверждения операции удаления группы ресурсов. Процесс удаления может занять несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)
