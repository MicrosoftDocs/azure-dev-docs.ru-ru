---
title: включить файл
description: включить файл
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 718ac8c480c5d366e985f9488d81ab626a82b586
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401568"
---
## <a name="reverse-a-terraform-execution-plan"></a>Отмена плана выполнения Terraform

1. Чтобы отменить план выполнения, выполните [terraform plan](https://www.terraform.io/docs/commands/plan.html) и укажите флаг `destroy` следующим образом:

    ```cmd
    terraform plan -destroy -out <terraform_plan>.destroy.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. Выполните команду [terraform apply](https://www.terraform.io/docs/commands/apply.html), чтобы применить план выполнения.

    ```cmd
    terraform apply <terraform_plan>.destroy.tfplan
    ```
