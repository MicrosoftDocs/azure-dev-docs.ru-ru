---
title: включить файл
description: включить файл
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 9fcde450a19515c38eb6653d75e644ca23d08964
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401569"
---
## <a name="create-a-base-terraform-configuration-file"></a>Создание базового файла конфигурации Terraform

Файл конфигурации Terraform начнет выполнение операций с указания поставщика. При использовании Azure в блоке `provider` вы укажете [поставщика Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html).

```terraform
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name = "<your_resource_group_name>"
  location = "<your_resource_group_location>"
}

# Your Terraform code goes here...

```

**Примечания**

- Хотя атрибут `version` является необязательным, HashiCorp рекомендует прикреплять его к предоставляемой версии поставщика. 
- Если вы используете поставщик Azure 1.x, блок `features` не допускается.
- Если вы используете поставщик Azure 2.x, блок `features` является обязательным.
- В [объявлении ресурса](https://www.terraform.io/docs/configuration/resources.html) [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) есть два аргумента: `name` и `location`. Задайте для заполнителей соответствующие значения для вашей среды.
- [Локальное именованное значение](https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values) из `rg` для группы ресурсов используется во всех статьях с инструкциями при ссылке на группу ресурсов. Это не зависит от имени группы ресурсов и относится только к имени переменной в вашем коде. Если вы измените это значение в определении группы ресурсов, также необходимо будет изменить его в коде, который на него ссылается.
