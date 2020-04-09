---
title: Руководство по настройке очередей в Служебной шине Azure с помощью Ansible
description: Узнайте, как создать очередь Служебной шины Azure с помощью Ansible
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 4054645f2252b1c972218707f58e4c661a26397f
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741022"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Руководство по Настройка очередей в Служебной шине Azure с помощью Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание очереди
> * Создание политики SAS
> * Получение сведений о пространстве имен
> * Получение сведений об очереди
> * Отзыв политики SAS очереди

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Создание очереди Служебной шины

Код из примера сборника схем создает следующие ресурсы:
- Группа ресурсов Azure
- пространство имен Служебной шины в группе ресурсов;
- очередь Служебной шины с пространством имен.

Сохраните следующий сборник схем как `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Создание политики SAS

[Подписанные URL-адреса (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) — это механизм авторизации на основе утверждений с использованием маркеров. 

Код из примера сборника схем создает две политики SAS для очереди служебной шины с разными привилегиями.

Сохраните следующий сборник схем как `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.
- Значение `rights` представляет привилегию пользователя при использовании очереди. Укажите одно из следующих значений: `manage`, `listen`, `send` или `listen_send`.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Получение сведений о пространстве имен

Код из примера сборника схем запрашивает сведения о пространстве имен.

Сохраните следующий сборник схем как `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.
- Значение `show_sas_policies` указывает, следует ли отображать политики SAS в указанном пространстве имен. Используется значение по умолчанию `False`, чтобы избежать дополнительных сетевых издержек.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Получение сведений об очереди

Код из сборника схем запрашивает сведения об очереди. 

Сохраните следующий сборник схем как `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.
- Значение `show_sas_policies` указывает, следует ли отображать политики SAS в указанной очереди. Используется значение по умолчанию `False`, чтобы избежать дополнительных сетевых издержек.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Отзыв политики SAS очереди

Код из примера сборника схем удаляет политику SAS очереди.

Сохраните следующий сборник схем как `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий код как `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"] 
> [Руководство. Настройка раздела в Служебной шине Azure с помощью Ansible](service-bus-topic-configure.md)