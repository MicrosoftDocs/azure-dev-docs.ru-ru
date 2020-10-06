---
title: включить файл
description: включить файл
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: daa57dfc18cae3250c42265ebe8cbf7722e4235b
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401593"
---
## <a name="configure-your-environment"></a>Настройка среды

С учетом своей среды установите и настройте Terraform:

- [Настройка Terraform с помощью Azure Cloud Shell и Azure CLI](../get-started-cloud-shell.md)
- [Настройка Terraform с помощью Azure PowerShell](../get-started-powershell.md)

В статьях, посвященных конфигурации, также показано, как выполнять следующие задачи:

- Создание базового файла конфигурации Terraform. Файл содержит [поставщик Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) в блоке `provider` и определяет [группу ресурсов Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group).
- Создание и применение плана выполнения Terraform для выполнения в коде.
- Отмена плана выполнения, когда вы закончите использовать ресурсы и захотите удалить их.
