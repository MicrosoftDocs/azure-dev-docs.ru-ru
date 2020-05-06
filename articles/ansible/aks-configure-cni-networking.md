---
title: Руководство по настройке сети Azure CNI в Службе Azure Kubernetes (AKS) с помощью Ansible
description: Узнайте, как с помощью Ansible настроить сеть Kubenet в кластере Службы Azure Kubernetes (AKS).
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, aks, контейнер, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 6be7056a89b0ac0327ffc5ba39b77c31c23bb0fe
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743906"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Руководство по настройке сети Azure CNI в Службе Azure Kubernetes (AKS) с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

С помощью AKS можно развернуть кластер, использующий одну из следующих моделей сети.

- [Сеть Kubenet](/azure/aks/configure-kubenet) — ресурсы сети обычно создаются и настраиваются при развертывании кластера AKS.
- [Сеть Azure CNI](/azure/aks/configure-azure-cni) — кластер AKS подключается к имеющимся ресурсам и конфигурациям виртуальных сетей.

Дополнительные сведения о сети см. в статье [Основные понятия сети в Службе Azure Kubernetes (AKS)](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание кластера AKS
> * Настройка сети Azure CNI

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети

Код из примера сборника схем в этом разделе используется для следующего.

- Создание виртуальной сети
- Создание подсети в виртуальной сети.

Сохраните следующий сборник схем как `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Создание кластера AKS в виртуальной сети

Код из примера сборника схем в этом разделе используется для следующего.

- Создание кластера создать в виртуальной сети.

Сохраните следующий сборник схем как `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Чтобы определить поддерживаемую версию, используйте модуль `azure_rm_aks_version`.
- Подсеть `vnet_subnet_id` была создана в предыдущем разделе.
- Сборник схем выполняет загрузку `ssh_key` из `~/.ssh/id_rsa.pub`. При его изменении следует использовать однострочный формат, который начинается с "ssh-rsa" (без кавычек).
- Значения `client_id` и `client_secret` загружаются из `~/.azure/credentials`, который является файлом учетных данных по умолчанию. Эти значения можно установить в качестве субъекта-службы или загрузить их значения из переменных среды.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Выполнение примера сборника схем

Код из примера сборника схем в этом разделе используется для проверки различных функций, показанных в этом учебнике.

Сохраните следующий сборник схем как `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Измените значение `aksansibletest`, указав имя своей группы ресурсов.
- Измените значение `aksansibletest`, указав имя службы AKS.
- Измените значение `eastus`, указав расположение группы ресурсов.

Чтобы запустить сборник схем, выполните команду ansible-playbook.

```bash
ansible-playbook aks-azure-cni.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Код из примера сборника схем в этом разделе используется для следующего.

- Удаление группы ресурсов, упомянутой в разделе `vars`.

Сохраните следующий сборник схем как `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Замените заполнитель `{{ resource_group_name }}` именем своей группы ресурсов.
- Будут удалены все ресурсы в указанной группе ресурсов.

Чтобы запустить сборник схем, выполните команду ansible-playbook.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. настройке Azure Active Directory в AKS с помощью Ansible](./aks-configure-rbac.md)