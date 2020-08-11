---
title: Руководство. Создание виртуальной сети концентратора в Azure с помощью Terraform
description: Узнайте, как создать центральную виртуальную сеть в Azure, которая будет выступать в качестве общей точки подключения между другими сетями.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ed2b391075dc04b7ef16d3f305ef4ff86abed4ae
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87478574"
---
# <a name="tutorial-create-a-hub-virtual-network-in-azure-by-using-terraform"></a>Руководство по созданию виртуальной сети концентратора в Azure с помощью Terraform

Виртуальная сеть концентратора выступает в качестве центральной точки подключения к локальной сети. В виртуальной сети размещены общие службы,которые используются рабочими нагрузками из виртуальных сетей периферийных зон. В целях демонстрации в примере, приведенном в этой статье, не реализуются общие службы.

В этой статье рассматриваются следующие задачи:

> [!div class="checklist"]
> * использование языка конфигурации HashiCorp (HCL) для реализации виртуальной сети в топологии по типу "звезда";
> * создание виртуальной машины концентратора, которая будет использоваться в качестве места перехода, с помощью Terraform;
> * создание шлюза виртуальной частной сети концентратора с помощью Terraform;
> * создание подключений к концентратору и локальному шлюзу с помощью Terraform.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

1. [Создание гибридной сети со звездообразной топологией с помощью Terraform в Azure](./hub-spoke-introduction.md).
1. [Создание локальной виртуальной сети с помощью Terraform в Azure](./hub-spoke-on-prem.md).

## <a name="create-the-directory-structure"></a>Создание структуры каталога

Сеть концентратора содержит следующие компоненты:

- виртуальная сеть концентратора;
- шлюз виртуальной сети концентратора;
- подключения шлюза концентратора. 

Файл конфигурации Terraform, указанный ниже, определяет ресурсы:

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/common/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Перейдите в новый каталог.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-virtual-network"></a>Объявление виртуальной сети концентратора

Создайте файл конфигурации Terraform, который объявляет виртуальную сеть концентратора.

1. В Cloud Shell создайте файл с именем `hub-vnet.tf`.

    ```bash
    code hub-vnet.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = local.hub-resource-group
      location = local.hub-location
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name
      address_space       = ["10.0.0.0/16"]

      tags = {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub
        subnet_id                     = azurerm_subnet.hub-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags = {
        environment = local.prefix-hub
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name   = azurerm_resource_group.hub-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nic.id]
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
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-hub
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.hub-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.hub-gateway-subnet.id
      }
      depends_on = [azurerm_public_ip.hub-vpn-gateway1-pip]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = azurerm_virtual_network_gateway.hub-vnet-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.onprem-vpn-gateway.id

      shared_key = local.shared-key
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = azurerm_virtual_network_gateway.onprem-vpn-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.hub-vnet-gateway.id

      shared_key = local.shared-key
    }
    ```
    
3. Сохраните файл и закройте редактор.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Создание устройства виртуальной сети концентратора с помощью Terraform в Azure](./hub-spoke-hub-nva.md)