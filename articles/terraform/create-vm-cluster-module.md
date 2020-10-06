---
title: Настройка кластера виртуальных машин Azure с помощью Terraform
description: Узнайте, как использовать модули Terraform для создания кластера виртуальных машин Windows в Azure.
keywords: Azure DevOps, Terraform, виртуальная машина, кластер виртуальных машин, реестр модулей
ms.topic: how-to
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 73f375090a2178b38b0fc7e0afd4eb8c6b514672
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401592"
---
# <a name="configure-an-azure-vm-cluster-using-terraform"></a>Настройка кластера виртуальных машин Azure с помощью Terraform

В этой статье показан пример кода Terraform для создания кластера виртуальных машин в Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-vm-cluster"></a>Настройка кластера виртуальных машин Azure

```hcl
module "windowsservers" {
  source              = "Azure/compute/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  is_windows_image    = true
  vm_hostname         = "mywinvm"                         // Line can be removed if only one VM module per resource group
  admin_password      = "ComplxP@ssw0rd!"                 // See note following code about storing passwords in config files
  vm_os_simple        = "WindowsServer"
  public_ip_dns       = ["winsimplevmips"]                // Change to a unique name per data center region
  vnet_subnet_id      = module.network.vnet_subnets[0]
    
  depends_on = [azurerm_resource_group.rg]
}

module "network" {
  source              = "Azure/network/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  subnet_prefixes     = ["10.0.1.0/24"]
  subnet_names        = ["subnet1"]

  depends_on = [azurerm_resource_group.rg]
}

output "windows_vm_public_name" {
  value = module.windowsservers.public_ip_dns_name
}

output "vm_public_ip" {
  value = module.windowsservers.public_ip_address
}

output "vm_private_ips" {
  value = module.windowsservers.network_interface_private_ip
}
```

**Примечания**

- В предыдущем примере кода переменной `admin_password` присваивается литеральное значение для простоты. Есть много способов хранения конфиденциальных данных, таких как пароли. Решение о том, как вы хотите защитить свои данные, сводится к индивидуальному выбору с учетом вашей конкретной среды и уровня комфорта при раскрытии этих данных. В качестве примера риска хранение такого файла в системе управления версиями может привести к тому, что пароль увидят другие. Дополнительные сведения по этой теме: HashiCorp предоставляет разные способы [объявления входных переменных](https://www.terraform.io/docs/configuration/variables.html) и методы [управления конфиденциальными данными (например, паролями)](https://www.terraform.io/docs/state/sensitive-data.html).

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)
