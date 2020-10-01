---
title: Руководство. Создание периферийной сети с помощью Terraform в Azure
description: Сведения о реализации двух периферийных виртуальных сетей (VNets), подключенных к концентратору, в звездообразной топологии.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: 265c410d8fc8bdb51803406cc8003e6e349014d9
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401484"
---
# <a name="tutorial-create-a-spoke-network-in-azure-using-terraform"></a>Руководство по Создание периферийной сети с помощью Terraform в Azure

В этом учебнике описано, как реализовать две отдельные периферийные сети, чтобы изолировать рабочие нагрузки. Эти сети используют общие ресурсы через центральную виртуальную сеть. Периферийные зоны можно использовать для изоляции рабочих нагрузок в их виртуальных сетях, управляемых отдельно от других периферийных зон. Каждая рабочая нагрузка может содержать несколько уровней с несколькими подсетями, подключенными через подсистемы балансировки нагрузки Azure.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * реализация периферийных виртуальных сетей в звездообразной топологии с помощью языка HCL;
> * создание виртуальных машин в периферийных сетях с помощью Terraform;
> * установление пиринговых связей виртуальной сети с сетями-концентраторами с помощью Terraform.

## <a name="prerequisites"></a>Предварительные требования

1. [Создайте гибридную сеть со звездообразной топологией с помощью Terraform в Azure](./hub-spoke-introduction.md).
1. [Создайте локальную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-on-prem.md).
1. [Создайте центральную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-hub-network.md).
1. [Создайте устройство центральной виртуальной сети с помощью Terraform в Azure](./hub-spoke-hub-nva.md).

## <a name="create-the-directory-structure"></a>Создание структуры каталога

В этом разделе описано, как создать скрипты двух периферийных сетей. Каждый скрипт определяет периферийную виртуальную сеть и виртуальную машину для рабочей нагрузки. Затем создается одноранговая виртуальная сеть между концентратором и периферийной сетью.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/common/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Перейдите в новый каталог:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>Объявление двух периферийных сетей

1. В Cloud Shell откройте новый файл с именем `spoke1.tf`.

    ```bash
    code spoke1.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = local.spoke1-resource-group
      location = local.spoke1-location
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke1-vnet-rg.name
      address_space       = ["10.1.0.0/16"]

      tags = {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke1-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = [azurerm_virtual_network.spoke1-vnet, azurerm_virtual_network.hub-vnet , azurerm_virtual_network_gateway.hub-vnet-gateway]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke1
        subnet_id                     = azurerm_subnet.spoke1-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke1-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke1-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke1-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = [azurerm_virtual_network.spoke1-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }
    ```

1. Сохраните файл и закройте редактор.

1. Создайте файл с именем `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Скопируйте приведенный ниже код и вставьте его в редактор.
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = local.spoke2-resource-group
      location = local.spoke2-location
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke2-vnet-rg.name
      address_space       = ["10.2.0.0/16"]

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke2-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = [azurerm_virtual_network.spoke2-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke2
        subnet_id                     = azurerm_subnet.spoke2-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke2-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke2-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke2-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = [azurerm_virtual_network.spoke2-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }
    ```
     
1. Сохраните файл и закройте редактор.
  
[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Проверка сети со звездообразной топологией с помощью Terraform в Azure](./hub-spoke-validation.md)