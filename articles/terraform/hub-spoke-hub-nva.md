---
title: Руководство. Создание устройства виртуальной сети концентратора в Azure с помощью Terraform
description: В этом руководстве описывается, как создать центральную виртуальную сеть, которая будет выступать в качестве общей точки подключения между другими сетями.
ms.topic: tutorial
ms.date: 03/08/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: b8deb50c29a0fd1cdd317dc2edfe0bd3bf21d8da
ms.sourcegitcommit: 7991f748720673d2dc50baaa8658348ff6cc1044
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604165"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-in-azure-using-terraform"></a>Руководство по Создание устройства виртуальной сети концентратора в Azure с помощью Terraform

*VPN-устройство* предоставляет возможность внешнего подключения к локальной сети. В качестве VPN-устройства может использоваться аппаратное устройство или программное решение. Один из примеров программного решения — это служба маршрутизации и удаленного доступа (RRAS) в Windows Server 2012. Дополнительные сведения о VPN-устройствах см. в статье [VPN-устройства и параметры IPsec/IKE для подключений типа "сеть — сеть" через VPN-шлюз](/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Azure поддерживает большое количество виртуальных сетевых устройств. В этой статье используется образ Ubuntu. Дополнительные сведения о поддерживаемых в Azure сетевых устройствах см. на [этой домашней странице](https://azure.microsoft.com/solutions/network-appliances/).

В этой статье рассматриваются следующие задачи:

> [!div class="checklist"]
> * реализация виртуальной сети концентратора в звездообразной топологии с помощью языка HCL;
> * создание виртуальной машины сети-концентратора, которая выступает как устройство, с помощью Terraform;
> * включение маршрутов с использованием расширений CustomScript с помощью Terraform;
> * создание таблицы маршрутов шлюзов со звездообразной топологией с помощью Terraform.

## <a name="prerequisites"></a>Предварительные требования

1. [Создайте гибридную сеть со звездообразной топологией с помощью Terraform в Azure](./hub-spoke-introduction.md).
1. [Создайте локальную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-on-prem.md).
1. [Создайте центральную виртуальную сеть с помощью Terraform в Azure](./hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>Создание структуры каталога

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

## <a name="declare-the-hub-network-appliance"></a>Объявление устройства сети-концентратора

Создайте файл конфигурации Terraform, который объявляет локальную виртуальную сеть.

1. В Cloud Shell создайте файл с именем `hub-nva.tf`.

    ```bash
    code hub-nva.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "eastus"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = local.hub-nva-location

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub-nva
        subnet_id                     = azurerm_subnet.hub-dmz.id
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = azurerm_resource_group.hub-nva-rg.location
      resource_group_name   = azurerm_resource_group.hub-nva-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nva-nic.id]
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
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      virtual_machine_id   = azurerm_virtual_machine.hub-nva-vm.id
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"


      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = azurerm_subnet.hub-gateway-subnet.id
      route_table_id = azurerm_route_table.hub-gateway-rt.id
      depends_on = [azurerm_subnet.hub-gateway-subnet]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke1-mgmt.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = [azurerm_subnet.spoke1-mgmt]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke1-workload.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = [azurerm_subnet.spoke1-workload]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags = {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke2-mgmt.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = [azurerm_subnet.spoke2-mgmt]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke2-workload.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = [azurerm_subnet.spoke2-workload]
    }
    ```

1. Сохраните файл и закройте редактор.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание периферийных виртуальных сетей с помощью Terraform в Azure](./hub-spoke-spoke-network.md)