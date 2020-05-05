---
title: Руководство. Хранение состояния Terraform в службе хранилища Azure
description: Узнайте, как сохранить сведения о состоянии Terraform в Службе хранилища Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: f2180a8e12632c0693dcf491d621121168fa99c8
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169700"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Руководство по Хранение состояния Terraform в службе хранилища Azure

Состояние Terraform используется для согласования развернутых ресурсов с конфигурациями Terraform. Благодаря состоянию Terraform известно, какие ресурсы Azure следует добавить, изменить или удалить. По умолчанию, состояние Terraform хранится локально при выполнении команды `terraform apply`. Такая конфигурация не является идеальной по следующим причинам.

- Локальное состояние плохо подходит для групповой среды или среды совместной работы.
- Состояние Terraform может включать в себя конфиденциальные сведения.
- Локальное хранение состояния повышает риск непреднамеренного удаления.

Terraform поддерживает сохранение состояния в удаленном хранилище. Один из поддерживаемых серверных компонентов — служба хранилища Azure. В этом документе показано, как настроить и использовать службу хранилища Azure для этой цели.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-storage-account"></a>Настройка учетной записи хранения

Прежде чем использовать службу хранилища Azure в качестве серверной части, необходимо создать учетную запись хранилища Azure. Это можно сделать на портале Azure, с помощью PowerShell, Azure CLI или самого средства Terraform. Чтобы настроить учетную запись хранения с помощью Azure CLI, воспользуйтесь приведенным ниже примером.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Запишите имя учетной записи хранения, имя контейнера и ключ доступа к хранилищу. Эти значения требуются при настройке удаленного состояния.

## <a name="configure-state-back-end"></a>Настройка серверного компонента состояния

Серверный компонент состояния Terraform настраивается при выполнении команды `terraform init`. Для настройки серверного компонента состояния необходимы следующие данные:

- **storage_account_name**: имя учетной записи хранения Azure.
- **container_name**: имя контейнера больших двоичных объектов.
- **key:** имя файла хранилища состояния, который необходимо создать.
- **access_key**: ключ доступа к хранилищу.

Каждое из этих значений можно указать в файле конфигурации Terraform или в командной строке. Для значения `access_key` рекомендуется использовать переменную среды. Это предотвращает запись ключа на диск.

Создайте переменную среды `ARM_ACCESS_KEY`, значением которой является ключ доступа к хранилищу Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Чтобы еще надежнее защитить ключ доступа к учетной записи хранения Azure, храните его в Azure Key Vault. В этом случае переменную среды можно задать с помощью команды, наподобие приведенной ниже. Дополнительные сведения об Azure Key Vault см. в [документации по Azure Key Vault](/azure/key-vault/secrets/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Чтобы настроить Terraform на использование серверного компонента, необходимо выполнить следующие действия.
- Включите блок конфигурации `backend` с типом `azurerm`.
- Добавьте значение `storage_account_name` в блок конфигурации.
- Добавьте значение `container_name` в блок конфигурации.
- Добавьте значение `key` в блок конфигурации.

В приведенном ниже примере настраивается сервер Terraform и создается группа ресурсов Azure.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Инициализируйте конфигурацию, выполнив приведенные ниже шаги.

1. Выполните команду `terraform init`.
1. Выполните команду `terraform apply`.

Теперь файл состояния можно найти в хранилище BLOB-объектов Azure.

## <a name="state-locking"></a>Блокировка состояния

Большие двоичные объекты службы хранилища Azure автоматически блокируются перед любой операцией, которая записывает состояние. Благодаря такому шаблону предотвращается параллельное выполнение операций с состоянием, которое может привести к повреждению. 

Дополнительные сведения см. в разделе [State Locking](https://www.terraform.io/docs/state/locking.html) (Блокировка состояния) в документации Terraform.

Блокировку можно увидеть при просмотре большого двоичного объекта на портале Azure или с помощью других средств управления Azure.

![BLOB-объект Azure с блокировкой](media/store-state-in-azure-storage/lock.png)

## <a name="encryption-at-rest"></a>Шифрование при хранении

Данные, хранящиеся в большом двоичном объекте Azure, шифруются перед сохранением. При необходимости Terraform получает состояние из серверного компонента и сохраняет его в локальной памяти. При использовании этого шаблона состояние никогда не записывается на локальный диск.

Дополнительные сведения о шифровании в службе хранилища Azure см. в статье [Шифрование службы хранилища Azure для неактивных данных](/azure/storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)
