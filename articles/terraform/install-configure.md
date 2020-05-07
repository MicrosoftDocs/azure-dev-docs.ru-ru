---
title: Краткое руководство. Установка и настройка Terraform для подготовки ресурсов Azure
description: Узнайте, как установить и настроить Terraform для создания ресурсов Azure.
keywords: Azure DevOps, Terraform, установка, настройка
ms.topic: quickstart
ms.date: 04/26/2020
ms.openlocfilehash: e395227171417ccf73ef073a6067732fb11a418d
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82169740"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Краткое руководство. Установка и настройка Terraform для подготовки ресурсов Azure
 
Terraform предоставляет простой способ определить, просмотреть и развернуть облачную инфраструктуру, используя [простой настраиваемый язык шаблонов](https://www.terraform.io/docs/configuration/syntax.html). В этой статье описываются шаги, необходимые для подготовки ресурсов Azure с помощью Terraform.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="install-terraform"></a>Установка Terraform

По умолчанию в [Azure Cloud Shell](/azure/cloud-shell/overview) установлена последняя версия Terraform. Если вы хотите установить Terraform локально, выполните следующий шаг. Если нет, перейдите к разделу [Настройка доступа Terraform к Azure](#configure-terraform-access-to-azure).

1. [Установите Terraform](https://www.terraform.io/downloads.html), указав соответствующий пакет для вашей операционной системы.
1. Пакет для скачивания содержит один исполняемый файл. Определите глобальный путь к исполняемому файлу в зависимости от операционной системы:
    - [Linux или MacOS](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux);
    - [Windows](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).
1. Проверьте конфигурацию глобального пути, выполнив команду `terraform`. Если средство Terraform найдено и работает, отобразится список его доступных параметров:

    ```console
    azureuser@Azure:~$ terraform
    Usage: terraform [--version] [--help] <command> [args]
    ```
    
## <a name="configure-terraform-access-to-azure"></a>Настройка доступа Terraform к Azure

Создайте [субъект-службу Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli), чтобы предоставить Terraform возможность подготовить ресурсы в Azure. Субъект-служба предоставляет сценарии Terraform для подготовки ресурсов в подписке Azure.

Если у вас есть несколько подписок Azure, сначала запросите свою учетную запись с помощью команды [az account list](/cli/azure/account#az-account-list), чтобы получить список значений идентификатора подписки и идентификатора клиента:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Чтобы использовать выбранную подписку, настройте подписку для этого сеанса с помощью команды [az account set](/cli/azure/account#az-account-set). Настройте переменную среды `SUBSCRIPTION_ID`, чтобы она хранила значение возвращаемого поля `id` из подписки, которую необходимо использовать.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Теперь вы можете создать субъект-службу для использования с Terraform. Используйте [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) и установите *область* для подписки следующим образом:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Будут возвращены `appId`, `password`, `sp_name` и `tenant`. Запишите `appId` и `password`.

## <a name="configure-terraform-environment-variables"></a>Настройка переменных среды Terraform

Чтобы настроить Terraform для использования субъекта-службы Azure AD, задайте следующие переменные среды, которые затем используются [модулями Azure Terraform](https://registry.terraform.io/modules/Azure). Можно также настроить среду, если используется другое облако Azure, а не с общедоступное облако Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Вы можете использовать этот пример сценария оболочки для установки этих переменных:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Запуск образца сценария

Создайте файл `test.tf` в пустом каталоге и вставьте следующий сценарий.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Сохраните файл, а затем инициализируйте развертывание Terraform. На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

```bash
terraform init
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Вы можете просмотреть действия, которые должны быть выполнены с помощью сценария Terraform, с помощью `terraform plan`. Когда вы будете готовы создать группу ресурсов, примените план Terraform следующим образом:

```bash
terraform apply
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы установили Terraform или использовали Cloud Shell, чтобы настроить учетные данные Azure и начать создание ресурсов в подписке Azure. Чтобы создать полное развертывание Terraform в Azure, перейдите к следующей статье:

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](create-linux-virtual-machine-with-infrastructure.md)