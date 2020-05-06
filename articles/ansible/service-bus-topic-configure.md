---
title: Руководство по настройке разделов в Служебной шине Azure с помощью Ansible
description: Узнайте, как создать раздел Служебной шины Azure с помощью Ansible.
keywords: ansible, azure, devops, bash, сборник схем, служебная шина, разделы, подписки
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1561a53a99954190aed71fd7d58c5535e68c00e9
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743298"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Руководство по настройке разделов в Служебной шине Azure с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание раздела
> * Создание подписки
> * Создание политики SAS
> * Получение сведений о пространстве имен
> * Получение сведений о разделе и подписке
> * Отмена политики SAS

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Создание раздела служебной шины

Код из примера сборника схем создает следующие ресурсы:
- Группа ресурсов Azure
- пространство имен Служебной шины в группе ресурсов;
- раздел служебной шины с пространством имен.

Сохраните следующий сборник схем как `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Создание подписки

Код из примера сборника схем создает подписку для раздела служебной шины. Разделы Служебной шины Azure могут иметь несколько подписок. Подписка на раздел может получать копию каждого сообщения, отправленного в этот раздел. Подписки — это именованные сущности, которые могут создаваться на неограниченный срок, но для них также можно настроить ограничение срока действия.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Сохраните следующий сборник схем как `servicebus_subscription.yml`:

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Создание политики SAS

[Подписанные URL-адреса (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) — это механизм авторизации на основе утверждений с использованием маркеров. 

Код из примера сборника схем создает две политики SAS для очереди служебной шины с разными привилегиями.

Сохраните следующий сборник схем как `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Получение сведений о разделе и подписке

Код из примера сборника схем запрашивает следующие сведения.
- Сведения о разделе служебной шины
- Вывод сведений о подписках для раздела
 
Сохраните следующий сборник схем как `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.
- Значение `show_sas_policies` указывает, следует ли отображать политики SAS в указанной очереди. Используется значение по умолчанию `False`, чтобы избежать дополнительных сетевых издержек.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Документация по Ansible в Azure](/azure/ansible/)