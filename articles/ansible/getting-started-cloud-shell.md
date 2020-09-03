---
title: Краткое руководство. Настройка Ansible с помощью Azure Cloud Shell
description: В этом кратком руководстве вы узнаете, как выполнять разные задачи Ansible с использованием Bash в Azure Cloud Shell.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 42d7b57d9890bce3a432fba3c6fdcf3080ddb63c
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275166"
---
# <a name="quickstart-configure-ansible-using-azure-cloud-shell"></a>Краткое руководство. Настройка Ansible с помощью Azure Cloud Shell

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

В этой статье описывается, как начать работу с Ansible из среды [Azure Cloud Shell](/azure/cloud-shell/overview).

## <a name="configure-your-environment"></a>Настройка среды

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Настройте Azure Cloud Shell**. Если вы еще не работали с Azure Cloud Shell, ознакомьтесь с [кратким руководством по использованию Bash в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [open-cloud-shell.md](../includes/open-cloud-shell.md)]

## <a name="automatic-credential-configuration"></a>Автоматическая настройка учетных данных

После входа в Cloud Shell Ansible выполняет проверку подлинности с помощью Azure для управления инфраструктурой без какой-либо дополнительной настройки. 

Если используется несколько подписок, укажите подписку для работы с Ansible, экспортировав переменную среды `AZURE_SUBSCRIPTION_ID`. 

Для получения списка всех подписок Azure выполните следующую команду.

```azurecli-interactive
az account list
```

С помощью своего идентификатора подписки Azure настройте `AZURE_SUBSCRIPTION_ID`, как показано ниже.

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Проверка конфигурации
Для проверки успешности конфигурации создайте группу ресурсов Azure с помощью Ansible.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Настройка виртуальных машин в Azure с помощью Ansible](./vm-configure.md)
