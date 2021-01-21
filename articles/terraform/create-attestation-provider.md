---
title: Настройка поставщика аттестации Azure с помощью Terraform
description: Узнайте, как создать поставщик аттестации в Azure с помощью Terraform.
keywords: Azure DevOps, Terraform, аттестация
ms.topic: how-to
ms.date: 11/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: c79c472da4604458475bd230a844e2d308c2bda1
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561550"
---
# <a name="configure-an-azure-attestation-policy-using-terraform"></a>Настройка политики аттестации Azure с помощью Terraform

В этой статье показан пример кода Terraform для создания [поставщика аттестации](/azure/attestation/overview) в Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- *Сертификат для подписи политики*: файл, указывающий набор доверенных ключей подписывания в виде PEM-файла.

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-attestation-provider"></a>Настройка поставщика аттестации Azure

```hcl
resource "azurerm_resource_group" "corpAttestation" {
  name                        = "corp-attestation"
  location                    = "westus"
}

resource "azurerm_attestation_provider" "corpAttestation" {
  name                              = "attestationprovider007"
  resource_group_name               = azurerm_resource_group.corpAttestation.name
  location                          = azurerm_resource_group.corpAttestation.location

  policy_signing_certificate_data   = file("./certs/cert.pem")
}
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)