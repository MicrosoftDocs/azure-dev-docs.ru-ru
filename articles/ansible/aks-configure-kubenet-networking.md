---
title: Руководство по настройке сети Kubenet в Службе Azure Kubernetes (AKS) с помощью Ansible
description: Узнайте, как с помощью Ansible настроить сеть Kubenet в кластере Службы Azure Kubernetes (AKS).
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, aks, контейнер, aks, kubernetes
ms.topic: tutorial
ms.custom: fasttrack-edit
ms.date: 10/23/2019
ms.openlocfilehash: 7d1dc7b381c02c84b2da89c5c90d822e86a3cd1b
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82026127"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Руководство по Настройка сети Kubenet в Службе Azure Kubernetes (AKS) с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

С помощью AKS можно развернуть кластер, использующий одну из следующих моделей сети.

- [Сеть Kubenet](/azure/aks/configure-kubenet) — ресурсы сети обычно создаются и настраиваются при развертывании кластера AKS.
- [Сеть Azure Container Networking Interface (CNI)](/azure/aks/configure-azure-cni) — кластер AKS подключен к имеющимся ресурсам виртуальной сети и конфигурациям.

Дополнительные сведения о сети см. в статье [Основные понятия сети в Службе Azure Kubernetes (AKS)](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание кластера AKS
> * Настройка сети Kubenet Azure

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети

В этом разделе код из сборника схем создает следующие ресурсы Azure:

- Виртуальная сеть
- Подсеть в виртуальной сети.

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

В этом разделе код из сборника схем создает виртуальную сеть в кластер AKS. 

Сохраните следующий сборник схем как `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Чтобы определить поддерживаемую версию, используйте модуль `azure_rm_aks_version`.
- Подсеть `vnet_subnet_id` была создана в предыдущем разделе.
- `network_profile` — определяет свойства подключаемого модуля сети Kubenet.
- `service_cidr` используется для назначения IP-адреса внутренним службам в кластере AKS. Этот диапазон IP-адресов не должен использоваться вне кластеров AKS. Но можно повторно использовать IP-адрес CIDR службы для нескольких кластеров AKS. 
- Адрес `dns_service_ip` должен быть адресом ".10" диапазона IP-адресов вашей службы.
- Диапазон `pod_cidr` должен быть большим адресным пространством, которое не используется где-либо еще в сетевой среде. Диапазон адресов должен быть достаточно большим, чтобы вместить количество узлов, до которых вы планируете масштабировать среду. После развертывания кластера этот диапазон адресов нельзя изменить. Как и в случае с CIDR службы, этот диапазон IP-адресов не должен существовать вне кластера AKS. Но его можно безопасно повторно использовать в кластерах.
- Диапазон IP-адресов объектов pod используется для назначения адресного пространства /24 каждому узлу в кластере. В приведенном ниже примере `pod_cidr` для 192.168.0.0/16 назначает первому узлу 192.168.0.0/24, второму узлу — 192.168.1.0/24 и третьему узлу —192.168.2.0/24.
- При масштабировании или обновлении кластера Azure продолжает назначать диапазон IP-адресов объектов pod каждому новому узлу.
- Сборник схем выполняет загрузку `ssh_key` из `~/.ssh/id_rsa.pub`. При его изменении следует использовать однострочный формат, который начинается с "ssh-rsa" (без кавычек).
- Значения `client_id` и `client_secret` загружаются из `~/.azure/credentials`, который является файлом учетных данных по умолчанию. Эти значения можно установить в качестве субъекта-службы или загрузить их значения из переменных среды.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Сопоставление сетевых ресурсов

При создании кластера AKS создается группа безопасности сети и таблица маршрутов. Эти ресурсы управляются AKS и обновляются при создании и размещении служб. Сопоставьте группу безопасности сети и таблицу маршрутов с подсетью виртуальной сети, как показано ниже: 

Сохраните следующий сборник схем в качестве `associate.yml`:

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- `node_resource_group` — имя группы ресурсов, в которой созданы узлы AKS.
- Подсеть `vnet_subnet_id` была создана в предыдущем разделе.


## <a name="run-the-sample-playbook"></a>Выполнение примера сборника схем

В этом разделе перечислены полные примеры сборника схем, который в этой статье вызывает создание задач. 

Сохраните следующий сборник схем как `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

В разделе `vars` необходимо выполнить следующие изменения.

- В ключе `resource_group` необходимо изменить значение `aksansibletest` на имя вашей группы ресурсов.
- В ключе `name` измените значение `aksansibletest` на ваше имя AKS.
- В ключе `Location` необходимо изменить значение `eastus` на расположение вашей группы ресурсов.

Запустите готовый сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook aks-kubenet.yml
```

После выполнения сборника схем отобразятся результаты, как показано ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий код как `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

В разделе `vars` замените заполнитель `{{ resource_group_name }}` именем вашей группы ресурсов.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Tutorial: Configure Azure CNI networking in Azure Kubernetes Service (AKS) using Ansible](./aks-configure-cni-networking.md) (Руководство. Настройка сети Azure CNI в Службе Azure Kubernetes (AKS) с помощью Ansible)
