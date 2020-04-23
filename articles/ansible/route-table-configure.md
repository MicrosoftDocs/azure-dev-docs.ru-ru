---
title: Руководство по настройке таблицы маршрутов Azure с помощью Ansible
description: Сведения о том, как создавать, администрировать и удалять таблицы маршрутов Azure с помощью Ansible. Узнайте также, как создавать и удалять маршруты.
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: cadc8f63d887cdd1ffe8b230dbe53a68645e601b
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743330"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Руководство по Настройка таблицы маршрутов Azure с помощью Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. При необходимости большего контроля над маршрутизацией среды можно создать [таблицу маршрутов](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Создание таблицы маршрутов. Создание виртуальной сети и подсети. Связывание таблицы маршрутов с подсетью. Отмена связывания таблицы маршрутов с подсетью. Создание и удаление маршрутов. Запрос таблицы маршрутов. Удаление таблицы маршрутов.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

Код сборника схем в этом разделе создает таблицу маршрутов. Дополнительные сведения об ограничениях таблицы маршрутов см. в разделе [Ограничения Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Сохраните следующий сборник схем как `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

В этом разделе код сборника схем:

* Создает виртуальную сеть.
* Создает подсеть в виртуальной сети.
* Связывает таблицы маршрутов с подсетью.

Таблицы маршрутов не связаны с виртуальными сетями. Таблицы маршрутов, скорее, связаны с подсетью виртуальной сети.

Виртуальная сеть и таблица маршрутов должны сосуществовать в одном расположении и подписке Azure.

Подсети и таблицы маршрутов имеют связь "один-ко-многим". Подсеть может быть определена без связанной таблицы маршрутов или только с одной таблицей маршрутов. Таблицы маршрутов могут быть связаны с одной, несколькими подсетями или ни с одной из них. 

Трафик из подсети маршрутизируется на основе:

- маршрутов, определенных в таблицах маршрутов
- [маршрутов по умолчанию](/azure/virtual-network/virtual-networks-udr-overview#default)
- маршрутов, которые распространяются из локальной сети.

Виртуальная сеть должна быть подключена к шлюзу виртуальной сети Azure. Шлюз может быть ExpressRoute или VPN, если используется BGP с VPN-шлюзом.

Сохраните следующий сборник схем как `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Отмена связи таблицы маршрутов с подсетью

Код сборника схем в этом разделе отменяет связь таблицы маршрутов с подсетью.

При отмене связи таблицы маршрутов с подсетью установите `route_table` для подсети на `None`. 

Сохраните следующий сборник схем как `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Создание маршрута

Код сборника схем в этом разделе — маршрут в таблице маршрутов. 

Сохраните следующий сборник схем как `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* `virtual_network_gateway` определяется как `next_hop_type`. Дополнительные сведения о том, как Azure выбирает маршруты, см. в разделе [Как Azure выбирает маршрут](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` определяется как `10.1.0.0/16`. Префикс не может быть продублирован в таблице маршрутов.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Удаление маршрута

Код сборника схем в этом разделе удаляет маршрут из таблицы маршрутов.

Сохраните следующий сборник схем как `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Получение информации о таблице маршрутов

Код сборника схем в этом разделе использует модуль Ansible `azure_rm_routetable_facts` для извлечения информации о таблице маршрутов.

Сохраните следующий сборник схем как `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Удаление таблицы маршрутов

Код сборника схем в этом разделе таблицы маршрутов.

После удаления таблицы маршрутов все ее маршруты также удаляются.

Таблица маршрутов не может быть удалена, если она связана с подсетью. Перед удалением таблицы маршрутов [отмените связь](#dissociate-a-route-table-from-a-subnet) таблицы маршрутов с любой подсетью. 

Сохраните следующий сборник схем как `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](/azure/ansible/)