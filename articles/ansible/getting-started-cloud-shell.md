---
title: Краткое руководство. Начало работы с Ansible — Azure Cloud Shell
description: В этом кратком руководстве вы узнаете, как выполнять разные задачи Ansible с использованием Bash в Azure Cloud Shell.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: 2d53c2c5e3eb834e0296be167104e484d144fbd9
ms.sourcegitcommit: 367217792f3b16c769e2c39372358bc6b9c9c044
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301155"
---
# <a name="quickstart-getting-started-with-ansible---azure-cloud-shell"></a>Краткое руководство. Начало работы с Ansible — Azure Cloud Shell

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