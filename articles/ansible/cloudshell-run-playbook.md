---
title: Краткое руководство по запуску сборников схем Ansible с помощью Bash в Azure Cloud Shell
description: В этом кратком руководстве вы узнаете, как выполнять разные задачи Ansible с использованием Bash в Azure Cloud Shell.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: b5c136d0eb6f3c226625c8a6d6c9200f6c283101
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741322"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Краткое руководство. Запуск сборников схем Ansible с помощью Bash в Azure Cloud Shell

Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure. Cloud Shell позволяет использовать командную строку Bash или PowerShell. В этой статье вы используете Bash в Azure Cloud Shell для запуска сборника схем Ansible.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Настройте Azure Cloud Shell**. Если вы еще не работали с Azure Cloud Shell, ознакомьтесь с [кратким руководством по использованию Bash в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Настройка виртуальных машин в Azure с помощью Ansible](./vm-configure.md)