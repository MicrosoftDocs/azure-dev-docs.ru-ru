---
title: Руководство по тестированию соответствия с помощью Terraform и Azure
description: Узнайте, как применить проверку соответствия в стиле разработки на основе поведения (BDD) к конфигурациям Terraform
ms.topic: tutorial
ms.date: 07/31/2020
ms.openlocfilehash: 38358be8bc7626d7b8a6b2df7e5da9b53d98618e
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810617"
---
# <a name="tutorial-compliance-testing-with-terraform-and-azure"></a>Руководство по тестированию соответствия с помощью Terraform и Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

В этой статье вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Узнайте, когда следует использовать тестирование соответствия
> * Узнайте, как выполнять тестирование соответствия.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Наличие Terraform.** [Скачайте и установите версию Terraform](https://www.terraform.io/downloads.html), которая соответствует вашей среде.
- **Docker**. [Установка Docker](https://docs.docker.com/get-docker/)
- **Terraform-compliance.** [Установите средство обеспечения соответствия требованиям для Terraform](https://terraform-compliance.com/pages/installation/docker).
- **Вилка примеров тестирования.** Создайте вилку [примера проекта Terraform на GitHub](https://github.com/Azure/terraform) и клонируйте его на локальный компьютер разработки и тестирования.

## <a name="what-is-compliance-testing"></a>Что такое тестирование соответствия

Тестирование соответствия — это методика нефункционального тестирования, которая позволяет определить, соответствует ли определенная система предписанным для нее стандартам. Проверка соответствия также называется *проверкой на совместимость*.

Большинство команд разработки программного обеспечения предусматривают некоторый анализ для проверки того, правильно ли применяются и реализуются стандарты. Часто одновременно с разработкой ведется и улучшение стандартов, что обеспечивает повышение качества.

Проверка соответствия гарантирует, что выходные данные каждого этапа жизненного цикла разработки соответствуют согласованным требованиям.

Проверки соответствия следует интегрировать в цикл разработки с самого начала работы над проектом. На более поздних этапах добавить проверки соответствия будет все сложнее и сложнее, особенно если требования недостаточно хорошо документированы.

## <a name="understanding-compliance-checks"></a>Общие сведения о проверке соответствия

Проверку соответствия выполнить очень просто. Создается и документируется набор стандартов и процедур для каждого этапа жизненного цикла разработки. Выходные данные каждого этапа сопоставляются с документированными требованиями. Результатами теста являются любые несоответствия между выходными данными и определенными заранее стандартами. Тестирование соответствия проводится на этапе проверки. Его результаты необходимо документировать.

Давайте рассмотрим конкретный пример.

Распространенной проблемой являются нарушения среды при одновременном применении несовместимых изменений несколькими разработчиками. Предположим, что один человек вносит изменения и применяет ресурсы, например создает виртуальную машину в тестовой среде. Другой разработчик затем применяет другую версию кода, которая подготавливает другую версию той же виртуальной машины. Для таких ситуаций важен контроль за единообразностью применения установленных правил.

Один из способов решения такой проблемы — политика добавления к ресурсам тегов, например `role` и `creator`. Определив все нужные политики, вы сможете проверять их соблюдение с помощью [Terraform-compliance](https://terraform-compliance.com) или других аналогичных средств.

Terraform-compliance предусматривает в первую очередь *отрицательное тестирование*. Отрицательное тестирование — это процесс, позволяющий гарантировать корректную обработку системой непредвиденных входных данных или нежелательных действий. Одним из методов отрицательного тестирования является *фаззинг*. При тестировании методом фаззинга система, принимающая входные данные, проверяется на возможность безопасно обрабатывать нестандартные входные данные.

К счастью, Terraform предоставляет уровень абстракции для любого API, который создает, обновляет или уничтожает сущности облачной инфраструктуры. Кроме того, Terraform обеспечивает синхронизацию локальной конфигурации и ответов удаленного API. Так как Terraform в основном используется для облачных API, нам нужен дополнительный способ проверять соответствие политикам для кода, развернутого в нашей инфраструктуре. Terraform-compliance — это бесплатное средство с открытым кодом, которое предоставляет эту возможность для конфигураций Terraform.

Для примера виртуальной машины политика соответствия может выглядеть следующим образом: *"Если создается ресурс Azure, он должен содержать тег"* .

Средство Terraform-compliance предоставляет платформу тестирования, в которой создаются подобные политики. Затем вы применяете эти политики к плану выполнения Terraform.

Terraform-compliance позволяет применять принципы *разработки на основе поведения* (BDD). Так называют процесс совместной работы, в котором все заинтересованные лица вместе пытаются определить, что должна делать система. Как правило, к заинтересованным лицам относятся разработчики, тестировщики и все, кого может заинтересовать разрабатываемая система или на чью деятельность она повлияет. Процесс BDD стимулирует разработчиков создавать конкретные примеры на основе общих представлений о том, как должна вести себя система.

## <a name="looking-at-an-example"></a>Рассмотрим конкретный пример

Ранее в этой статье уже приводился пример проверки соответствия для создания виртуальной машины в тестовой среде. В рамках этого раздела мы преобразуем этот пример в функцию и сценарий BDD. Сначала правило выражается с помощью специального средства *Cucumber* для поддержки BDD.

```Cucumber
when creating Azure resources, every new resource should have a tag
```

Описанное выше правило здесь выражается следующим образом:

```Cucumber
If the resource supports tags
Then it must contain a tag
And its value must not be null
```

Затем в коде Terraform HCL будет применяться это правило, как описано ниже.

```hcl
resource "random_uuid" "uuid" {}

resource "azurerm_resource_group" "rg" {
  name     = "rg-hello-tf-${random_uuid.uuid.result}"
  location = var.location

  tags = {
    environment = "dev"
    application = "Azure Compliance"
  } 
}
```

Первая политика выражается как [сценарий функции BDD](https://cucumber.io/docs/gherkin/reference/) следующим образом:

```Cucumber
Feature: Test tagging compliance  # /target/src/features/tagging.feature
    Scenario: Ensure all resources have tags
        If the resource supports tags
        Then it must contain a tag
        And its value must not be null
```

В следующем коде представлен тест наличия определенного тега:

```Cucumber
Scenario Outline: Ensure that specific tags are defined
    If the resource supports tags
    Then it must contain a tag <tags>
    And its value must match the "<value>" regex

    Examples:
      | tags        | value              |
      | Creator     | .+                 |
      | Application | .+                 |
      | Role        | .+                 |
      | Environment | ^(prod\|uat\|dev)$ |
```

## <a name="running-the-sample"></a>Выполнение примера

В рамках этого раздела вы скачаете и протестируете пример.

1. [Скачайте пример теста соответствия](https://github.com/Azure/terraform/tree/master/samples/compliance-testing).

1. Перейдите в каталог `src`.

1. Выполните команду [terraform init](https://www.terraform.io/docs/commands/init.html), чтобы инициализировать рабочий каталог. На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

    ```bash
    terraform init
    ```
    
1. Выполните [terraform validate](https://www.terraform.io/docs/commands/validate.html), чтобы проверить синтаксис файлов конфигурации.

    ```bash
    terraform validate
    ```
    
1. Чтобы создать план выполнения, выполните [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```bash
    terraform plan -out tf.out
    ```
    
1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```bash
    terraform apply -target=random_uuid.uuid
    ```
    
1. Выполните [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ terraform-compliance.

    ```bash
    docker pull eerkunt/terraform-compliance
    ```
    
1. Выполните [docker run](https://docs.docker.com/engine/reference/commandline/run/), чтобы провести тесты в контейнере Docker. **Этот тест завершится ошибкой**. Первое правило, которое требует наличия тегов, соблюдается. Но второе правило возвращает ошибку, так как отсутствуют теги `Role` и `Creator`.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```
    
    ![Пример непройденного теста](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-fail.png)

1. Измените `main.tf` указанным ниже образом, чтобы исправить эту ошибку.

    ```terraform
      tags = {
        Environment = "dev"
        Application = "Azure Compliance"
        Creator     = "Azure Compliance"
        Role        = "Azure Compliance"
      } 
    
    ```
    
1. Снова выполните `terraform validate`, чтобы проверить синтаксис.

    ```bash
    terraform validate
    ```
    
1. Снова выполните `terraform plan`, чтобы создать новый план выполнения.

    ```bash
    terraform plan -out tf.out
    ```
    
1. Снова выполните [docker run](https://docs.docker.com/engine/reference/commandline/run/), чтобы проверить конфигурацию. На этот раз тест будет выполнен успешно, так как определенная спецификация реализована полностью.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```

    ![Пример успешного прохождения теста](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-succeed.png)

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Создание и выполнение комплексных тестов в проектах Terraform](best-practices-end-to-end-testing.md)
