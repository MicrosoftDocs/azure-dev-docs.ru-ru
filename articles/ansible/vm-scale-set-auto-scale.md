---
title: Руководство по автомасштабированию масштабируемых наборов виртуальных машин в Azure с помощью Ansible
description: Узнайте, как использовать Ansible для масштабирования масштабируемого набора виртуальных машин с помощью функции автомасштабирования в Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 32e7f8029a361f9559a61a32bb078b2bd3a3ecd5
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240686"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Руководство по автомасштабированию масштабируемых наборов виртуальных машин в Azure с помощью Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

Функция автоматической настройки количества экземпляров виртуальных машин называется [автомасштабированием](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Преимуществом автомасштабирования является то, что на снижает расходы на управление, связанные с мониторингом и оптимизацией производительности приложения. Автомасштабирование можно настроить для работы в режиме по запросу или по определенному расписанию. С помощью Ansible можно указать правила автомасштабирования, определяющие допустимую производительность для комфортной работы клиента.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Определение профиля автомасштабирования
> * Автомасштабирование на основе повторяющегося расписания
> * Автомасштабирование в зависимости от производительности приложения
> * Получение сведений о параметрах автомасштабирования 
> * Отключение параметра автомасштабирования

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Автомасштабирование на основе расписания

Чтобы включить автомасштабирование в масштабируемом наборе, сначала необходимо определить профиль автомасштабирования. Этот профиль определяет используемую по умолчанию, минимальную и максимальную емкости масштабируемого набора. Эти ограничения позволяют контролировать затраты за счет того, что экземпляры виртуальных машин не создаются непрерывно, и сбалансировать приемлемую производительность с минимальным числом экземпляров, которые остаются в масштабируемом событии. 

Ansible позволяет изменять масштаб масштабируемых наборов в определенную дату или согласно повторяющемуся расписанию.

Код из сборника схем в этом разделе код увеличивает количество экземпляров виртуальных машин до трех в 10:00 каждый понедельник.

Сохраните следующий сборник схем как `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Автомасштабирование на основе данных о производительности

При увеличении потребностей приложения в масштабируемом наборе увеличивается нагрузка на экземпляры виртуальной машины. Если такая увеличенная нагрузка представляет собой не просто краткий спрос, а является согласованной, можно настроить правила автомасштабирования, чтобы увеличить число экземпляров виртуальной машины в масштабируемом наборе. После создания этих экземпляров виртуальных машин и развертывания приложений масштабируемые наборы запускают распределение трафика между ними через подсистему балансировки нагрузки. Ansible позволяет выбирать отслеживаемые метрики, например метрики загрузки ЦП, использования диска и времени загрузки приложений. Масштаб масштабируемых наборов можно горизонтально увеличивать и уменьшать на основе пороговых значений метрик производительности по расписанию или в определенную дату. 

Код из сборника схем в этом разделе проверяет загрузку ЦП за предыдущие 10 минут в 18:00 каждый понедельник. 

Исходя из процентных значений метрик ЦП, сборник схем выполняет одно из следующих действий:

- увеличивает количество экземпляров виртуальных машин до четырех;
- уменьшает количество экземпляров виртуальных машин до одной.

Сохраните следующий сборник схем как `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Получение сведений о параметрах автомасштабирования 

Код сборника схем в этом разделе использует модуль `azure_rm_autoscale_facts` для извлечения сведений о параметрах автомасштабирования.

Сохраните следующий сборник схем как `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Отключение параметров автомасштабирования

Отключить параметры автомасштабирования можно двумя способами. Один из них — изменить значение ключа `enabled` с `true` на `false`. Второй способ — удалить параметр.

Код из сборника схем в этом разделе удаляет параметр автомасштабирования. 

Сохраните следующий сборник схем как `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Руководство. Обновление пользовательского образа масштабируемых наборов виртуальных машин Azure с помощью Ansible](./vm-scale-set-update-image.md)