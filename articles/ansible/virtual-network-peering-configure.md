---
title: Руководство по настройке пиринга виртуальных сетей Azure с помощью Ansible
description: Узнайте, как установить пиринговое подключение между виртуальными сетями с помощью Ansible.
keywords: ansible, azure, devops, bash, сборник схем, схемы, пиринг
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2b5af7546900c45dde7eea0e1d57370d2ffc3991
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743882"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Руководство по настройке пиринга виртуальных сетей Azure с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[Пиринг виртуальных сетей](/azure/virtual-network/virtual-network-peering-overview) позволяет без дополнительной настройки соединить две виртуальные сети Azure. После создания пиринга две виртуальные сети выглядят как одна при подключении. 

Трафик передается между виртуальными машинами в одной виртуальной сети через частные IP-адреса. Точно так же трафик между виртуальными машинами в пиринговой виртуальной сети передается через магистральную инфраструктуру Майкрософт. Поэтому виртуальные машины в разных виртуальных сетях могут взаимодействовать между собой.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * создание двух виртуальных сетей;
> * Создание пирингового подключения между двумя виртуальными сетями.
> * Удаление пирингового подключения между двумя сетями.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Создание двух групп ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

Код из примера сборника схем в этом разделе используется для следующего.

- Создание двух групп ресурсов 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Создание первой виртуальной сети

Код из примера сборника схем в этом разделе используется для следующего.

- Создание виртуальной сети
- Создание подсети в виртуальной сети.

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Создание второй виртуальной сети

Код из примера сборника схем в этом разделе используется для следующего.

- Создание виртуальной сети
- Создание подсети в виртуальной сети.

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Создание пирингового подключения между двумя виртуальными сетями.

Код из примера сборника схем в этом разделе используется для следующего.

- Инициализация пиринга между виртуальными сетями.
- Создание пирингового подключения между двумя ранее созданными виртуальными сетями.

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Удаление пиринга виртуальных сетей

Код из примера сборника схем в этом разделе используется для следующего.

- Удаление пиринга между двумя ранее созданными виртуальными сетями.

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Получение примера сборника схем

Существует два способа получения полного примера сборника схем:

- [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) и сохраните его в `vnet_peering.yml`.
- Создайте файл с именем `vnet_peering.yml` и скопируйте в него следующее содержимое.

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Выполнение примера сборника схем

Код из примера сборника схем в этом разделе используется для проверки различных функций, показанных в этом учебнике.

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- В разделе `vars` замените заполнитель `{{ resource_group_name }}` именем вашей группы ресурсов.

Чтобы запустить сборник схем, выполните команду ansible-playbook.

```bash
ansible-playbook vnet_peering.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Код из примера сборника схем в этом разделе используется для следующего.

- Удаление двух групп ресурсов, созданных ранее.

Сохраните следующий сборник схем как `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Замените заполнитель `{{ resource_group_name-1 }}` именем первой созданной группы ресурсов.
- Замените заполнитель `{{ resource_group_name-2 }}` именем второй созданной группы ресурсов.
- Будут удалены все ресурсы в указанных группах ресурсов.

Чтобы запустить сборник схем, выполните команду ansible-playbook.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](/azure/ansible/)