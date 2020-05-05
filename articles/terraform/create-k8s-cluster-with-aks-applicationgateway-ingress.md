---
title: Руководство. Создание контроллера входящего трафика Шлюза приложений в Службе Azure Kubernetes
description: Узнайте, как создать кластер Kubernetes с помощью Службы Azure Kubernetes со Шлюзом приложений в качестве контроллера входящего трафика.
keywords: Azure DevOps, Terraform, Шлюз приложений, входящий трафик, AKS, Kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 95a1e9ebbf0dcffb2b3101c3b794ba86e62886c8
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82171250"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Руководство по Создание контроллера входящего трафика Шлюза приложений в Службе Azure Kubernetes

[Служба Azure Kubernetes (AKS)](/azure/aks/) управляет размещенной средой Kubernetes. AKS позволяет быстро и легко развернуть и администрировать контейнерные приложения даже без опыта в оркестрации контейнеров. AKS также устраняет нагрузку при переводе приложений в автономный режим для рабочих задач и задач обслуживания. Благодаря AKS эти задачи (включая подготовку, обновление и масштабирование ресурсов) могут выполняться по запросу.

Контроллер входящего трафика предоставляет различные функции для служб Kubernetes. Эти функции включают обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку завершения подключений TLS. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес. [Шлюз приложений](/azure/Application-Gateway/) Azure обеспечивает все эти функции, поэтому он является идеальным контроллером входящего трафика для Kubernetes в Azure. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание кластера [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) с помощью AKS со Шлюзом приложений в качестве контроллера входящего трафика.
> * Определение кластера Kubernetes с помощью языка HCL.
> * Создание ресурса Шлюза приложений с помощью Terraform.
> * Создание кластера Kubernetes с помощью AKS и Terraform.
> * Проверка доступности кластера Kubernetes с помощью средства kubectl.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Настройка Terraform.** Следуйте указаниям в статье [Terraform и настройка доступа к Azure](install-configure.md).

- **Группа ресурсов Azure**. Если у вас нет группы ресурсов Azure, которую можно использовать для демонстрационной версии, [создайте группу ресурсов Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Запишите имя и расположение группы ресурсов, так как эти значения используются в демонстрационной версии.

- **Субъект-служба Azure.** Следуйте указаниям, приведенным в разделе **Создание субъекта-службы** статьи [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Запишите значения для appId, displayName и password.

- **Получите идентификатор объекта субъекта-службы**: Выполните следующую команду в Cloud Shell: `az ad sp list --display-name <displayName>`.

## <a name="create-the-directory-structure"></a>Создание структуры каталога

Первый шаг — создание каталога, содержащего файлы конфигурации Terraform для упражнения.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Перейдите в новый каталог:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Объявление поставщика Azure

Создайте файл конфигурации Terraform, который объявляет поставщик Azure.

1. В Cloud Shell создайте файл с именем `main.tf`.

    ```bash
    code main.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="define-input-variables"></a>Определение входных переменных

Создайте файл конфигурации Terraform, в котором перечислены все переменные, необходимые для этого развертывания.

1. В Cloud Shell создайте файл с именем `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="define-the-resources"></a>Определение ресурсов 
Создайте файл конфигурации Terraform, который позволяет создать все ресурсы. 

1. В Cloud Shell создайте файл с именем `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Вставьте следующий блок кода, чтобы создать блок локальных переменных для вычисленных переменных и использовать их повторно.

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Вставьте следующий блок кода, чтобы создать источник данных для группы ресурсов с новым удостоверением пользователя.

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Вставьте следующий блок кода, чтобы создать базовые сетевые ресурсы.

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Вставьте следующий блок кода, чтобы создать ресурс Шлюза приложений.

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Вставьте следующий блок кода, чтобы создать назначения ролей.

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Вставьте следующий блок кода, чтобы создать кластер Kubernetes.

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

Код, представленный в этом разделе, задает имя кластера (name), расположение (location) и имя группы ресурсов (resource_group_name). Задается значение `dns_prefix`, которое составляет часть полного доменного имени (FQDN), используемого для доступа к кластеру.

Запись `linux_profile` позволяет настроить параметры, разрешающие вход на рабочие узлы с использованием SSH.

С AKS вы платите только за рабочие узлы. Запись `agent_pool_profile` настраивает сведения этих рабочих узлов. Запись `agent_pool_profile record` включает в себя количество и тип создаваемых рабочих узлов. Если в будущем потребуется увеличить или уменьшить масштаб кластера, следует изменить значение `count` в этой записи.

## <a name="create-a-terraform-output-file"></a>Создание выходного файла Terraform

[Выходные данные Terraform](https://www.terraform.io/docs/configuration/outputs.html) позволяют определить значения, которые выделяются для пользователя при применении плана Terraform, и их можно запрашивать с помощью команды `terraform output`. В этом разделе создается выходной файл, который обеспечивает доступ к кластеру с помощью [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. В Cloud Shell создайте файл с именем `output.tf`.

    ```bash
    code output.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Настройка хранилища Azure для хранения данных о состоянии Terraform

Terraform отслеживает состояние локально через файл `terraform.tfstate`. Эта схема хорошо работает в среде с одним пользователем. Но в более практичной многопользовательской среде необходимо отслеживать состояние на сервере с использованием [хранилища Azure](/azure/storage/). В этом разделе описано, как получать необходимые сведения об учетной записи хранения и создавать контейнер хранилища. Затем сведения о состоянии Terraform сохраняются в этом контейнере.

1. На портале Azure в разделе **Службы Azure**выберите **Учетные записи хранения**. (Если параметр **Учетные записи хранения** не отображается на главной странице, выберите **Другие службы**, найдите и выберите его.)

1. На странице **Учетные записи хранения** выберите имя учетной записи хранения, в которой будет сохраняться состояние Terraform. Например, можно использовать учетную запись хранения, созданную при открытии Cloud Shell в первый раз.  Имя учетной записи хранения, созданной с помощью Cloud Shell, обычно начинается с `cs`, после чего следует случайная строка из цифр и букв. 

    Запишите выбранную учетную запись хранения, так как она понадобится в дальнейшем.

1. На странице учетной записи хранения выберите **Ключи доступа**.

    ![Меню учетной записи хранения](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Запишите значение ключа **key1** **key**. (Если щелкнуть значок справа от ключа, значение будет скопировано в буфер обмена.)

    ![Ключи доступа к учетной записи хранения](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Создайте контейнер в учетной записи хранения Azure в Cloud Shell. Замените заполнители соответствующими для вашей учетной записи хранения Azure значениями.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Создание кластера Kubernetes
В этом разделе описывается использование команды `terraform init` для создания ресурсов, определяемых файлами конфигурации, которые вы создали в предыдущих разделах.

1. Инициализируйте Terraform в Cloud Shell. Замените заполнители соответствующими для вашей учетной записи хранения Azure значениями.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Команда `terraform init` отображает успешный результат инициализации внутренней части и подключаемого модуля поставщика:

    ![Пример результатов выполнения команды terraform init](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. В Cloud Shell создайте файл с именем `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Вставьте в редактор переменные, созданные ранее. Чтобы получить значение расположения для среды, используйте `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Выполните команду `terraform plan`, чтобы создать план Terraform, определяющий элементы инфраструктуры. 

    ```bash
    terraform plan -out out.plan
    ```

    Команда `terraform plan` отображает ресурсы, которые создаются при выполнении команды `terraform apply`:

    ![Пример результатов выполнения команды terraform plan](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Выполните команду `terraform apply`, чтобы применить план для создания кластера Kubernetes. Процесс создания кластера Kubernetes может занять несколько минут. В результате может быть превышено время ожидания сеанса Cloud Shell. Если время ожидания сеанса Cloud Shell истечет, можно выполнить действия из раздела "Восстановление после истечения времени ожидания Cloud Shell", что позволит завершить работу с руководством.

    ```bash
    terraform apply out.plan
    ```

    Команда `terraform apply` отображает результаты создания ресурсов, определенных в файлах конфигурации:

    ![Пример результатов выполнения команды terraform apply](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. На портале Azure выберите **Группы ресурсов** в меню слева, чтобы просмотреть ресурсы, созданные для нового кластера Kubernetes в выбранной группе ресурсов.

    ![Командная строка Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Восстановление после истечения времени ожидания Cloud Shell

Если время ожидания сеанса Cloud Shell истечет, можно выполнить следующие действия для восстановления:

1. Запустите сеанс Cloud Shell.

1. Перейдите в каталог, содержащий файлы конфигурации Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Выполните следующую команду:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Проверка кластера Kubernetes
Средства Kubernetes позволяют проверить только что созданный кластер.

1. Получите конфигурацию Kubernetes из данных о состоянии Terraform и сохраните ее в файле, который может прочитать средство kubectl.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Задайте переменную среды, позволяющую kubectl подобрать правильную конфигурацию.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Проверьте работоспособность кластера.

    ```bash
    kubectl get nodes
    ```

    Должны отобразиться сведения о рабочих узлах. У всех узлов должно быть состояние **Готово**, как показано на следующем рисунке:

    ![Средство kubectl позволяет проверить работоспособность кластера Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Установка идентификатора Pod Azure AD

Идентификатор Pod Azure Active Directory предоставляет доступ на основе маркеров к [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

[Идентификатор Pod Azure AD](https://github.com/Azure/aad-pod-identity) добавляет следующие компоненты в кластер Kubernetes:

  - [определения пользовательских ресурсов](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) Kubernetes: `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`;
  - компонент [Контроллер управляемых удостоверений (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic);
  - компонент [Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi).

Если доступ RBAC **включен**, выполните следующую команду, чтобы установить идентификатор Pod Azure AD в кластере:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Если доступ RBAC **отключен**, выполните следующую команду, чтобы установить идентификатор Pod Azure AD в кластере:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Установка Helm

В этом разделе код использует [Helm](/azure/aks/kubernetes-helm) (диспетчер пакетов Kubernetes) для установки пакета `application-gateway-kubernetes-ingress`:

1. Если доступ RBAC **включен**, выполните следующий набор команд, чтобы установить и настроить Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Если доступ RBAC **отключен**, выполните следующую команду, чтобы установить и настроить Helm:

    ```bash
    helm init
    ```

1. Добавьте репозиторий Helm AGIC:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Установка диаграммы Helm контроллера входящего трафика

1. Скачайте `helm-config.yaml`, чтобы настроить AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Измените `helm-config.yaml` и введите соответствующие значения для разделов `appgw` и `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    Значения описаны ниже.

    - `verbosityLevel`: задает уровень детализации инфраструктуры ведения журналов AGIC. Возможные значения можно найти в разделе [Уровни ведения журнала](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
    - `appgw.subscriptionId`: идентификатор подписки Azure Шлюза приложений. Например, `a123b234-a3b4-557d-b2df-a0bc12de1234`.
    - `appgw.resourceGroup`: имя группы ресурсов Azure, в которой был создан Шлюз приложений. 
    - `appgw.name`: имя Шлюза приложений. Например, `applicationgateway1`.
    - `appgw.shared`: этот логический флаг должен иметь значение по умолчанию `false`. Задайте значение `true`, если вам нужен [общий Шлюз приложений](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: укажите пространство имен, которое должно отслеживать AGIC. Пространство имен может быть одиночным строковым значением или разделенным запятыми списком пространств имен. Если оставить эту переменную с комментарием или установить ее в свободную или пустую строку, контроллер входящего трафика будет наблюдать за всеми доступными пространствами имен.
    - `armAuth.type`: значение `aadPodIdentity` или `servicePrincipal`.
    - `armAuth.identityResourceID`: идентификатор ресурса управляемого удостоверения.
    - `armAuth.identityClientId`: идентификатор клиента удостоверения.
    - `armAuth.secretJSON`: требуется только при выборе типа секрета субъекта-службы (если для параметра `armAuth.type` установлено значение `servicePrincipal`).

    Важные примечания:
    - Значение `identityResourceID` создается в скрипте Terraform и его можно найти, выполнив команду: `echo "$(terraform output identity_resource_id)"`.
    - Значение `identityClientID` создается в скрипте Terraform и его можно найти, выполнив команду: `echo "$(terraform output identity_client_id)"`.
    - Значение `<resource-group>` — это группа ресурсов Шлюза приложений.
    - Значение `<identity-name>` — это имя созданного удостоверения.
    - Все удостоверения для этой подписки могут быть перечислены с помощью `az identity list`.

1. Установите пакет контроллера входящего трафика Шлюза приложений:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Установка примера приложения

После установки Шлюза приложений, AKS и AGIC вы можете установить пример приложения через [Azure Cloud Shell](https://shell.azure.com/):

1. Чтобы скачать файл YAML, используйте команду cURL:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Примените файл YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.  

Замените заполнитель соответствующим значением. Будут удалены все ресурсы в указанной группе ресурсов.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Контроллер входящего трафика Шлюза приложений](https://azure.github.io/application-gateway-kubernetes-ingress/)