---
title: развертыванию Azure Cosmos DB в службе "Экземпляры контейнеров Azure"
description: Узнайте, как использовать Terraform для развертывания Azure Cosmos DB в Экземплярах контейнеров Azure
ms.topic: how-to
ms.date: 04/26/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 376d0e3637489ddedde942812dcbf55d46da48db
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241166"
---
# <a name="deploy-an-azure-cosmos-db-to-azure-container-instances"></a>развертыванию Azure Cosmos DB в службе "Экземпляры контейнеров Azure"

В этой статье описывается, как создать ресурсы Azure с помощью Terraform. Описанные в этой статье действия помогут вам создать приведенные ниже ресурсы.

> [!div class="checklist"]
> * Экземпляр базы данных Azure Cosmos DB
> * Экземпляр контейнера Azure
> * Приложение, работающее в этих двух ресурсах

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="create-first-configuration"></a>Создание первой конфигурации

В этом разделе будет создана конфигурация для экземпляра Azure Cosmos DB.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте Azure Cloud Shell.

1. Запустите редактор Cloud Shell:

    ```bash
    code main.tf
    ```

1. В конфигурации этого шага создается макет нескольких ресурсов Azure. К этим ресурсам относятся группа ресурсов Azure и экземпляр базы данных Azure Cosmos DB. Для создания уникального имени экземпляра базы данных Cosmos DB используется случайное целое число. Кроме того, настраивается несколько параметров Cosmos DB. Дополнительные сведения см. в [справочных материалах по Terraform Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Скопируйте следующую конфигурацию Terraform и вставьте ее в редактор.

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

## <a name="run-the-configuration"></a>Запуск конфигурации

В этом разделе вы используете несколько команд Terraform для запуска конфигурации.

1. Команда [terraform init](https://www.terraform.io/docs/commands/init.html) инициализирует рабочий каталог. Выполните следующую команду в Cloud Shell:

    ```bash
    terraform init
    ```

1. Команду [terraform plan](https://www.terraform.io/docs/commands/plan.html) можно использовать для проверки синтаксиса конфигурации. Параметр `-out` направляет результаты в файл. Выходной файл можно использовать позже, чтобы применить конфигурацию. Выполните следующую команду в Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Команда [terraform apply](https://www.terraform.io/docs/commands/apply.html) предназначена для применения конфигурации. Указан выходной файл из предыдущего шага. Эта команда приводит к созданию ресурсов Azure. Выполните следующую команду в Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Перейдите к новой группе ресурсов, чтобы проверить результаты на портале Azure. Новый экземпляр базы данных Azure Cosmos DB находится в новой группе ресурсов.

## <a name="update-configuration"></a>Обновление конфигурации

В этом разделе приведены сведения об обновлении конфигурации для включения Экземпляра контейнера Azure. Контейнер запускает приложение, которое считывает и записывает данные в Cosmos DB.

1. Запустите редактор Cloud Shell:

    ```bash
    code main.tf
    ```

1. На этом шаге конфигурация устанавливает две переменные среды: `COSMOS_DB_ENDPOINT` и `COSMOS_DB_MASTERKEY`. Эти переменные содержат расположение и ключ для доступа к базе данных. Значения этих переменных получены из экземпляра базы данных, созданного на предыдущем шаге. Этот процесс известен как интерполяция. Дополнительные сведения об интерполяции Terraform см. в документации по [синтаксису интерполяции](https://www.terraform.io/docs/configuration/interpolation.html). Конфигурация также включает в себя блок выходных данных, который возвращает полное доменное имя экземпляра контейнера. Скопируйте следующий код и вставьте его в редактор:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Как и в предыдущем разделе, выполните следующую команду, чтобы отобразить изменения, которые необходимо внести:

    ```bash
    terraform plan --out plan.out
    ```

1. Наконец, запустите команду `terraform apply`, чтобы применить конфигурацию.

    ```bash
    terraform apply plan.out
    ```

1. Запишите полное доменное имя экземпляра контейнера.

## <a name="test-application"></a>Тестирование приложения

Перейдите к полному доменному имени экземпляра контейнера, чтобы проверить приложение. Должен отобразиться похожий результат:

![Приложение Azure для голосования](media/deploy-azure-cosmos-db-to-azure-container-instances/azure-vote.jpg)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны.

Чтобы удалить ресурсы Azure, созданные при работе с этой статьей, выполните команду [terraform destroy](https://www.terraform.io/docs/commands/destroy.html).

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Install and configure Terraform to provision Azure resources](get-started-cloud-shell.md) (Установка и настройка Terraform для подготовки ресурсов Azure)