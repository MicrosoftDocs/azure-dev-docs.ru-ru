---
title: Руководство. Создание масштабируемого набора виртуальных машин Azure с помощью Terraform
description: Узнайте, как использовать Terraform для настройки и модификации масштабируемого набора виртуальных машин Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: bb6175c92d0487bd5707b721ebc39ce4b727fed6
ms.sourcegitcommit: aa417af8b5f00cbc056666e481250ef45c661d52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153722"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Руководство по созданию масштабируемого набора виртуальных машин Azure с помощью Terraform

[Масштабируемые наборы виртуальных машин Azure](/azure/virtual-machine-scale-sets) позволяют настроить идентичные виртуальные машины. Количество экземпляров виртуальных машин можно настроить по запросу или расписанию. Дополнительные сведения см. в статье [Автоматическое масштабирование масштабируемого набора виртуальных машин на портале Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

В этом руководстве описано следующее:

> [!div class="checklist"]
> * настройка развертывания Terraform;
> * использование переменных и выходных данных для развертывания Terraform;
> * создание и развертывание сетевой инфраструктуры;
> * создание и развертывание масштабируемого набора виртуальной машины и присоединение его к сети;
> * создание и развертывание Jumpbox для подключения к виртуальным машинам по протоколу SSH.

> [!NOTE]
> Последнюю версию файлов конфигурации Terraform, используемых в этой статье, можно скачать из [репозитория Awesome Terraform на GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Наличие Terraform.** Следуйте указаниям в статье [Terraform и настройка доступа к Azure](install-configure.md).

- **Наличие пары ключей SSH.** Дополнительные сведения см. в статье [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Создание структуры каталога

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `vmss`.

    ```bash
    mkdir vmss
    ```

1. Перейдите в новый каталог:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Создание файла определений переменных
Работая с этим разделом, вы определите переменные, с помощью которых можно настроить ресурсы, созданные Terraform.

В Azure Cloud Shell выполните указанные ниже действия.

1. Создайте файл с именем `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = map

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Создание выходного файла определений
Работая с этим разделом, вы создадите файл, в котором описаны выходные данные после развертывания.

В Azure Cloud Shell выполните указанные ниже действия.

1. Создайте файл с именем `output.tf`.

    ```bash
    code output.tf
    ```

1. Вставьте приведенный ниже код в редактор, чтобы предоставить сведения о полном доменном имени (FQDN) виртуальным машинам.
   , перечислены ниже.

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Определение сетевой инфраструктуры в шаблоне
Работая с этим разделом, вы создадите указанную ниже сетевую инфраструктуру в новой группе ресурсов Azure.

  - Одна виртуальная сеть с диапазоном адресов 10.0.0.0/16.
  - Одна подсеть с пространством адресов 10.0.2.0/24.
  - Два общедоступных IP-адреса. Один используется подсистемой балансировки нагрузки масштабируемого набора виртуальных машин, другой — для подключения к SSH Jumpbox.

В Azure Cloud Shell выполните указанные ниже действия.

1. Создайте файл с именем `vmss.tf` для описания инфраструктуры масштабируемого набора виртуальных машин.

    ```bash
    code vmss.tf
    ```

1. Вставьте приведенный ниже код в конец файла, чтобы предоставить сведения о полном доменном имени (FQDN) виртуальным машинам.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
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
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Подготовка сетевой инфраструктуры
С помощью Azure Cloud Shell в каталоге, в котором вы создали файлы конфигурации (TF-файлы), выполните указанные ниже действия.

1. Инициализируйте Terraform.

   ```bash
   terraform init
   ```

1. Разверните определенную инфраструктуру в Azure, выполнив указанную ниже команду.

   ```bash
   terraform apply
   ```

   Terraform запросит значение `location`, так как переменная `location` определена в файле `variables.tf`, но еще не задана. Вы можете ввести любое допустимое расположение, например западная часть США (западная часть США), и нажать клавишу ВВОД. (Значения с пробелами необходимо заключить в круглые скобки.)

1. Terraform печатает выходные данные, как задано в файле `output.tf`. Как показано на снимке экрана ниже, полное доменное имя имеет следующий вид: `<ID>.<location>.cloudapp.azure.com`. Здесь идентификатор — это вычисленное значение, а расположение — значение, указанное при запуске Terraform.

   ![Полное доменное имя масштабируемого набора виртуальных машин для общедоступного IP-адреса](./media/create-vm-scaleset-network-disks-hcl/fqdn.png)

1. В главном меню на портале Azure выберите **Группы ресурсов**.

1. Чтобы отобразить список ресурсов, созданных Terraform, на вкладке **Группы ресурсов** щелкните **myResourceGroup**.
   ![Сетевые ресурсы масштабируемого набора виртуальных машин](./media/create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Добавление масштабируемого набора виртуальных машин

Работая с этим разделом, вы научитесь добавлять указанные ниже ресурсы в шаблон.

- Подсистема балансировки нагрузки Azure и правила для предоставления приложения и присоединения его к общедоступному IP-адресу, созданному ранее в этой статье
- Внутренний пул адресов Azure и назначение его подсистеме балансировки нагрузки
- Порт проверки работоспособности, используемый приложением и настраиваемый в подсистеме балансировки нагрузки.
- Масштабируемый набор виртуальных машин, защищенный подсистемой балансировки нагрузки, которая работает в виртуальной сети, развертывание которой описано ранее в этой статье
- [Nginx](https://nginx.org/) на узлах масштабируемого набора виртуальных машин, для которого используется [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

В Azure Cloud Shell выполните указанные ниже действия.

1. Откройте файл конфигурации `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Перейдите в конец файла и перейдите в режим добавления, нажав клавишу A.

1. Добавьте следующий код в конец файла:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
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
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = var.tags
   }
   ```

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1. Создайте файл `web.conf`, который будет использоваться в качестве конфигурации cloud-init для виртуальных машин, входящих в масштабируемый набор.

    ```bash
    code web.conf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

     ```bash
     :wq
     ```

1. Откройте файл конфигурации `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Перейдите в конец файла и перейдите в режим добавления, нажав клавишу A.

1. Настройте развертывание, вставив следующий код в конец файла:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Создайте план Terraform, чтобы визуализировать развертывание масштабируемого набора виртуальных машин. (Вам потребуется указать выбранный вами пароль, а также расположение для ваших ресурсов.)

    ```bash
    terraform plan
    ```

    Выходные данные этой команды должны быть приблизительно такие, как на снимке экрана ниже.

    ![Выходные данные при создании масштабируемого набора виртуальных машин](./media/create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Разверните новые ресурсы в Azure.

    ```bash
    terraform apply
    ```

    Выходные данные этой команды должны быть приблизительно такие, как на снимке экрана ниже.

    ![Группа ресурсов масштабируемого набора виртуальных машин Terraform](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Откройте браузер и подключитесь к полному доменному имени, которое было возвращено командой.

    ![Результаты перехода по полному доменному имени](./media/create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Добавление точки перехода SSH
*jumpbox* SSH — это единый сервер, с помощью которого можно перейти на другие серверы в сети. На этом шаге настраиваются следующие ресурсы:

- Сетевой интерфейс (или точка перехода) подключен к той же подсети, что и масштабируемый набор виртуальных машин.

- Виртуальная машина подключена с помощью данного сетевого интерфейса. jumpbox доступен в удаленном режиме. После подключения можно использовать SSH для подключения к любой из виртуальных машин в масштабируемом наборе.

1. Откройте файл конфигурации `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Перейдите в конец файла и перейдите в режим добавления, нажав клавишу A.

1. Добавьте следующий код в конец файла:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
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

    tags = var.tags
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
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Откройте файл конфигурации `output.tf`.

   ```bash
   code output.tf
   ```

1. Перейдите в конец файла и перейдите в режим добавления, нажав клавишу A.

1. Вставьте указанный ниже код в конец файла, чтобы отобразить имя узла точки перехода по завершении развертывания.

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Разверните jumpbox.

   ```bash
   terraform apply
   ```

По завершении развертывания содержимое группы ресурсов выглядит, как показано на снимке экрана ниже.

![Группа ресурсов масштабируемого набора виртуальных машин Terraform](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Возможность выполнения входа с указанием пароля отключена в точке перехода и в развернутом вами масштабируемом наборе виртуальных машин. Чтобы получить доступ к виртуальным машинам, выполните вход с помощью SSH.

## <a name="environment-cleanup"></a>Очистка среды

Чтобы удалить ресурсы Terraform, которые вы создали при работе с этим руководством, введите следующую команду в Cloud Shell:

```bash
terraform destroy
```

Процесс удаления может занять несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)
