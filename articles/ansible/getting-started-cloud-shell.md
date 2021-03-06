---
title: Краткое руководство. Настройка Ansible с помощью Azure Cloud Shell
description: В этом кратком руководстве вы узнаете, как выполнять разные задачи Ansible с использованием Bash в Azure Cloud Shell.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: ddc4ba9f473e5e8d727996a4e902210f0d3055f6
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831190"
---
# <a name="quickstart-configure-ansible-using-azure-cloud-shell"></a>Краткое руководство. Настройка Ansible с помощью Azure Cloud Shell

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

В этой статье описывается, как начать работу с Ansible из среды [Azure Cloud Shell](/azure/cloud-shell/overview).

## <a name="configure-your-environment"></a>Настройка среды

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Настройте Azure Cloud Shell**. Если вы еще не работали с Azure Cloud Shell, ознакомьтесь с [кратким руководством по использованию Bash в Azure Cloud Shell](/azure/cloud-shell/quickstart).

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

## <a name="test-ansible-installation"></a>Проверка установки Ansible

Теперь вы настроили Ansible для использования в Cloud Shell.

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Настройка виртуальных машин в Azure с помощью Ansible](./vm-configure.md)