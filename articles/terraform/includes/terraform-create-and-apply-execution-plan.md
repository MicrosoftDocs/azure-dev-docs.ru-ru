---
title: включить файл
description: включить файл
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 23a5bbc2e6a7e364b3c6eab38bfd5e98b97348a6
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401570"
---
## <a name="create-and-apply-a-terraform-execution-plan"></a>Создание и применение плана выполнения Terraform

В этом разделе показано, как создать *план выполнения* и применить его к облачной инфраструктуре.

1. Чтобы инициализировать развертывание Terraform, выполните команду [terraform init](https://www.terraform.io/docs/commands/init.html). Эта команда скачивает модули Azure, необходимые для создания группы ресурсов Azure.

    ```cmd
    terraform init
    ```

1. После инициализации создайте план выполнения, выполнив [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```cmd
    terraform plan -out <terraform_plan>.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. Когда вы будете готовы применить план выполнения к облачной инфраструктуре, выполните [terraform apply](https://www.terraform.io/docs/commands/apply.html).

    ```cmd
    terraform apply <terraform_plan>.tfplan
    ```
