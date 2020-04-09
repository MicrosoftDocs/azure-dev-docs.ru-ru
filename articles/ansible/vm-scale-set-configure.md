---
title: Руководство по настройке масштабируемых наборов виртуальных машин в Azure с помощью Ansible
description: Узнайте, как с помощью Ansible для создавать и настраивать масштабируемые наборы виртуальных машин в Azure.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: dbaffec218d9af6f41b86faba496a229b64fa50b
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741512"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Руководство по настройке масштабируемых наборов виртуальных машин в Azure с помощью Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Настройка ресурсов для виртуальной машины.
> * Настройка масштабируемого набора.
> * Масштабирование масштабируемого набора путем увеличения числа экземпляров виртуальных машин. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Настройка масштабируемого набора.

Код из сборника схем в этом разделе определяет следующие ресурсы:

* **Группа ресурсов**, в которую будут развернуты все ваши ресурсы.
* **Виртуальная сеть** в адресном пространстве 10.0.0.0/16.
* **Подсеть** в виртуальной сети.
* **Общедоступный IP-адрес**, который позволяет получить доступ к ресурсам через Интернет.
* **Группа безопасности сети**, которая контролирует поток исходящего и входящего трафика масштабируемого набора.
* **Подсистема балансировки нагрузки**, которая распределяет трафик в наборе определенных виртуальных машин с помощью правил подсистемы балансировки нагрузки.
* **Масштабируемый набор виртуальных машин**, который использует все созданные ресурсы.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) и сохраните его в `vmss-create.yml`.
* Создайте файл с именем `vmss-create.yml` и скопируйте в него следующее содержимое.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* В разделе `vars` замените заполнитель `{{ admin_password }}` собственным паролем.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vmss-create.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Просмотр числа экземпляров виртуальных машин

Сейчас [настроенный масштабируемый набор](#configure-a-scale-set) содержит два экземпляра. Чтобы убедиться в этом, можно сделать следующее.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Перейдите к масштабируемому набору, который вы настроили.

1. Вы увидите имя масштабируемого набора с числом экземпляров в скобках: `Standard_DS1_v2 (2 instances)`.

1. Вы можете также проверить число экземпляров с помощью [Azure Cloud Shell](https://shell.azure.com/), выполнив следующую команду.

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    После выполнения команды в Cloud Shell видно, что теперь в масштабируемом наборе три экземпляра. 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Горизонтальное увеличение масштаба масштабируемого набора

Код из сборника схем в этом разделе извлекает информацию о масштабируемом наборе и изменяет его емкость с двух виртуальных машин до трех.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) и сохраните его в `vmss-scale-out.yml`.
* Создайте файл с именем `vmss-scale-out.yml` и скопируйте в него следующее содержимое.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vmss-scale-out.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Проверка результатов

Проверьте результаты работы на портале Azure.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Перейдите к масштабируемому набору, который вы настроили.

1. Вы увидите имя масштабируемого набора с числом экземпляров в скобках: `Standard_DS1_v2 (3 instances)`. 

1. Вы также можете проверить изменение с помощью [Azure Cloud Shell](https://shell.azure.com/), выполнив следующую команду:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    После выполнения команды в Cloud Shell видно, что теперь в масштабируемом наборе три экземпляра. 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Руководство. развертыванию приложений в масштабируемых наборах виртуальных машин в Azure c помощью Ansible](./vm-scale-set-deploy-app.md)