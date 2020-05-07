---
title: Руководство. Создание кластера Kubernetes с помощью службы Azure Kubernetes и Terraform
description: Узнайте, как создать кластер Kubernetes с помощью Службы Azure Kubernetes и Terraform.
keywords: Azure DevOps, Terraform, AKS, Kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 45a27dcd8751484f71c7a89f08109d2303ea121e
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82171150"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Руководство по созданию кластера Kubernetes с помощью Службы Azure Kubernetes и Terraform

[Служба Azure Kubernetes (AKS)](/azure/aks/) управляет размещенной средой Kubernetes. AKS позволяет развернуть и администрировать контейнерные приложения даже без опыта в оркестрации контейнеров. AKS также позволяет выполнять множество общих операций обслуживания без перевода приложения в автономный режим. Эти операции включают подготовку, обновление и масштабирование ресурсов по требованию.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * определение кластера Kubernetes с помощью языка HCL;
> * создание кластера Kubernetes с помощью AKS и Terraform;
> * проверка доступности кластера Kubernetes с помощью средства kubectl.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Настройка Terraform.** Следуйте указаниям в статье [Terraform и настройка доступа к Azure](install-configure.md).

- **Субъект-служба Azure.** Следуйте инструкциям по **созданию субъекта-службы** в руководстве по [созданию субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Запишите значения appId (идентификатор приложения), displayName (отображаемое имя), password (пароль) и tenant (клиент).

## <a name="create-the-directory-structure"></a>Создание структуры каталога

Первый шаг — создание каталога, содержащего файлы конфигурации Terraform для упражнения.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Перейдите в новый каталог:

    ```bash
    cd terraform-aks-k8s
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

## <a name="define-a-kubernetes-cluster"></a>Определение кластера Kubernetes

Создайте файл конфигурации Terraform, который объявляет ресурсы для кластера Kubernetes.

1. В Cloud Shell создайте файл с именем `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Предыдущий код задает имя кластера (name), расположение (location) и имя группы ресурсов (resource_group_name). Также задается префикс полного доменного имени (FQDN). Полное доменное имя используется для доступа к кластеру.

    Запись `linux_profile` позволяет настроить параметры, разрешающие вход на рабочие узлы с использованием SSH.

    С AKS вы платите только за рабочие узлы. Запись `default_node_pool` настраивает сведения этих рабочих узлов. Запись `default_node_pool record` включает в себя количество и тип создаваемых рабочих узлов. Если в будущем потребуется увеличить или уменьшить масштаб кластера, следует изменить значение `count` в этой записи.

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Объявление переменных

1. В Cloud Shell создайте файл с именем `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Создание выходного файла Terraform

[Выходные данные Terraform](https://www.terraform.io/docs/configuration/outputs.html) позволяют определить значения, которые будут выделяться для пользователя при применении плана Terraform, и их можно запрашивать с помощью команды `terraform output`. В этом разделе создается выходной файл, который обеспечивает доступ к кластеру с помощью [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

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
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Настройка хранилища Azure для хранения данных о состоянии Terraform

Terraform отслеживает состояние локально через файл `terraform.tfstate`. Эта схема хорошо работает в среде с одним пользователем. В среде с несколькими пользователями для отслеживания состояния используется [служба хранилища Azure](/azure/storage/).

В этом разделе вы узнаете как выполнять указанные ниже задачи.
- Получение сведений об учетной записи хранения (имя учетной записи и ключ учетной записи)
- Создание контейнера хранилища, в котором будут храниться сведения о состоянии Terraform.

1. На портале Azure выберите **Все службы** в меню слева.

1. Выберите **Учетные записи хранения**.

1. На вкладке **Учетные записи хранения** выберите имя учетной записи хранения, в которой будет сохраняться состояние Terraform. Например, можно использовать учетную запись хранения, созданную при открытии Cloud Shell в первый раз.  Имя учетной записи хранения, созданной с помощью Cloud Shell, обычно начинается с `cs`, после чего следует случайная строка из цифр и букв. Запишите выбранную учетную запись хранения. Это значение потребуется позже.

1. На вкладке учетной записи хранения выберите **Ключи доступа**.

    ![Меню учетной записи хранения](./media/create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Запишите значение ключа **key1** **key**. (Если щелкнуть значок справа от ключа, значение будет скопировано в буфер обмена.)

    ![Ключи доступа к учетной записи хранения](./media/create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Создайте контейнер в учетной записи хранения Azure в Cloud Shell. Замените заполнители на соответствующие значения для вашей среды.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Создание кластера Kubernetes

В этом разделе показано, как использовать команду `terraform init` для создания ресурсов, определяемых файлами конфигурации, которые вы создали в предыдущих разделах.

1. Инициализируйте Terraform в Cloud Shell. Замените заполнители на соответствующие значения для вашей среды.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Команда `terraform init` отображает успешный результат инициализации внутренней части и подключаемого модуля поставщика:

    ![Пример результатов выполнения команды terraform init](./media/create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Экспортируйте учетные данные субъекта-службы. Замените заполнители на соответствующие значения из субъект-службы.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Выполните команду `terraform plan`, чтобы создать план Terraform, определяющий элементы инфраструктуры. 

    ```bash
    terraform plan -out out.plan
    ```

    Команда `terraform plan` отображает ресурсы, которые будут созданы при выполнении команды `terraform apply`:

    ![Пример результатов выполнения команды terraform plan](./media/create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Выполните команду `terraform apply`, чтобы применить план для создания кластера Kubernetes. Процесс создания кластера Kubernetes может занять несколько минут. В результате может быть превышено время ожидания сеанса Cloud Shell. Если время ожидания сеанса Cloud Shell истечет, можно выполнить действия из раздела "Восстановление после истечения времени ожидания Cloud Shell", что позволит завершить работу с руководством.

    ```bash
    terraform apply out.plan
    ```

    Команда `terraform apply` отображает результаты создания ресурсов, определенных в файлах конфигурации:

    ![Пример результатов выполнения команды terraform apply](./media/create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. На портале Azure выберите **Все ресурсы** в меню слева, чтобы просмотреть ресурсы, созданные для нового кластера Kubernetes.

    ![Все ресурсы на портале Azure](./media/create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

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

    ![Средство kubectl позволяет проверить работоспособность кластера Kubernetes](./media/create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Мониторинг работоспособности и журналов

После создания кластера AKS для сбора метрик работоспособности узлов кластера и модулей pod включается мониторинг. Эти метрики работоспособности доступны на портале Azure. Дополнительные сведения о мониторинге работоспособности контейнеров см. в разделе [Включение мониторинга для существующих управляемых кластеров](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)