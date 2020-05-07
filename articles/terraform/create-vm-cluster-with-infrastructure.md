---
title: Руководство. Создание кластера виртуальных машин Azure с помощью Terraform и HCL
description: Научитесь создавать кластер виртуальной машины Linux с подсистемой балансировки нагрузки в Azure с помощью Terraform и HCL.
keywords: Azure DevOps, Terraform, виртуальная машина, кластер виртуальных машин
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 7f19d28270f911cac7bc96a9159f1b927ceb0235
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82170480"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Руководство по созданию кластера виртуальных машин Azure с помощью Terraform и HCL

В этом руководстве показано, как создать небольшой вычислительный кластер с помощью [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Вы узнаете, как выполнять указанные далее задачи.

> [!div class="checklist"]
> * Настройка аутентификации Azure.
> * Создание файла конфигурации Terraform.
> * Создание файла конфигурации Terraform для создания подсистемы балансировки нагрузки.
> * Использование файла конфигурации Terraform для развертывания двух виртуальных машин Linux в группе доступности.
> * Инициализируйте Terraform.
> * Создание плана выполнения Terraform.
> * Примените план выполнения Terraform, чтобы создать ресурсы Azure.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="1-create-a-terraform-configuration-file"></a>1. Создание файла конфигурации Terraform

В этом разделе вы создадите файл с определениями ресурсов для инфраструктуры.

1. Создайте файл с именем `main.tf`. 

2. Скопируйте в новый файл `main.tf` следующие примеры определений ресурсов: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="2-initialize-terraform"></a>2. Инициализация Terraform

Команда [terraform init](https://www.terraform.io/docs/commands/init.html) позволяет инициализировать каталог, содержащий файлы конфигурации Terraform, которые вы создали в предыдущих разделах. Команду `terraform init` рекомендуется выполнять после каждого изменения конфигурации Terraform. 

> [!TIP]
> Команда `terraform init` является идемпотентной, то есть может выполняться несколько раз, возвращая одинаковые результаты. Таким образом, если в вашей среде применяются методы совместной работы и у вас есть основания полагать, что файлы конфигурации могли измениться, вызов команды `terraform init` перед выполнением или применением плана всегда будет полезным и безопасным.

Чтобы инициализировать Terraform, выполните следующую команду:

  ```bash
  terraform init
  ```

  ![Инициализация Terraform](media/create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="3-create-a-terraform-execution-plan"></a>3. Создание плана выполнения Terraform

Команда [terraform plan](https://www.terraform.io/docs/commands/plan.html) создает план выполнения. Чтобы создать план выполнения, Terraform анализирует и обобщает все файлы `.tf` в текущем каталоге. 

Параметр [out](https://www.terraform.io/docs/commands/plan.html#out-path) сохраняет план выполнения в выходной файл. Эта функция решает проблемы параллелизма, характерные для сред с поддержкой нескольких разработчиков. Одной из таких проблем, решаемых выходным файлом, является указанный ниже сценарий.

1. Dev 1 создает файл конфигурации.
1. Dev 2 изменяет файл конфигурации.
1. Dev 1 применяет (выполняет) файл конфигурации.
1. Dev 1 получает непредвиденные результаты, не зная, что Dev 2 изменил конфигурацию.

Dev 1, указывающий выходной файл, предотвращает влияние Dev 2 на Dev 1. 

Если вы не хотите сохранять план выполнения, выполните следующую команду:

  ```bash
  terraform plan
  ```

Если вы хотите сохранить план выполнения, выполните команду, указанную ниже. Замените заполнители на соответствующие значения для вашей среды.

  ```bash
  terraform plan -out=<path>
  ```

Другой полезный параметр — [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

По умолчанию Terraform пытается найти файл переменных следующим образом.
- Файл с именем `terraform.tfvars`
- Файл с именем, используя шаблон: `*.auto.tfvars`

Однако файл переменных не должен следовать одному из двух приведенных выше соглашений. В этом случае укажите имя файла переменных с параметром `-var-file`, в котором имя файла переменной не имеет расширения имени файла. Проиллюстрируем это на примере.

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform определяет действия, необходимые для достижения состояния, указанного в файле конфигурации.

![Создание плана выполнения Terraform](media/create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="4-apply-the-terraform-execution-plan"></a>4. Применение плана выполнения Terraform

Последним действием в этом руководстве будет команда [terraform apply](https://www.terraform.io/docs/commands/apply.html), которая применяет набор действий, созданных командой `terraform plan`.

Если вы хотите применить последний созданный план выполнения, выполните такую команду:

  ```bash
  terraform apply
  ```

Если вы хотите применить ранее сохраненный план выполнения, выполните такую команду: Замените заполнители на соответствующие значения для вашей среды.

  ```bash
  terraform apply <path>
  ```

![Применение плана выполнения Terraform](media/create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Создание масштабируемого набора виртуальных машин Azure с помощью Terraform](create-vm-scaleset-network-disks-hcl.md)