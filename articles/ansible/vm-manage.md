---
title: Краткое руководство. Управление виртуальными машинами Linux в Azure с помощью Ansible
description: Из этого краткого руководства вы узнаете, как с помощью Ansible управлять виртуальной машиной Linux в Azure.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 354c54191d108dc7a58f2813225a40b75968a4b2
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240626"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Краткое руководство. Управление виртуальными машинами Linux в Azure с помощью Ansible

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье описано, как запустить и остановить виртуальную машину Linux с помощью сборника схем Ansible. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Остановка виртуальной машины

В этом разделе вы отмените выделение (остановите) виртуальной машины Azure с помощью Ansible.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл `azure-vm-stop.yml` и откройте его в редакторе.

    ```bash
    code azure-vm-stop.yml
    ```

1. Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Замените заполнители `{{ resource_group_name }}` и `{{ vm_name }}` собственными значениями.

1. Сохраните файл и закройте редактор.

1. Запустите сборник схем с помощью команды `ansible-playbook`.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>запуск виртуальной машины

В этом разделе вы запустите остановленную виртуальную машину Azure (т. е. выделение которой было отменено) с помощью Ansible.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл `azure-vm-start.yml` и откройте его в редакторе.

    ```bash
    code azure-vm-start.yml
    ```

1. Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Замените заполнители `{{ resource_group_name }}` и `{{ vm_name }}` собственными значениями.

1. Сохраните файл и закройте редактор.

1. Запустите сборник схем с помощью команды `ansible-playbook`.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Руководство. управлению динамическими списками Azure с помощью Ansible](./dynamic-inventory-configure.md)