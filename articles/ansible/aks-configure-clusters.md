---
title: Руководство по настройке кластеров Службы Azure Kubernetes в Azure с помощью Ansible
description: Узнайте, как использовать Ansible для создания кластеров службы Azure Kubernetes и управления ими в Azure.
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, aks, контейнер, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7f68399fe2bf8dfe60a80ffdaff714b5c609b166
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743702"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Руководство по настройке кластеров Службы Azure Kubernetes в Azure с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

Службу Azure Kubernetes можно настроить для использования [Azure Active Directory (AD)](/azure/active-directory/) для аутентификации пользователей. После настройки можно будет использовать маркер аутентификации Azure AD для входа в кластер AKS. Управление доступом на основе ролей может основываться на удостоверении пользователя или членстве в группах каталога.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание кластера AKS
> * Настройка кластера AKS.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Создание управляемого кластера AKS

Пример сборника схем создает группу ресурсов и кластер AKS, который находится в этой группе ресурсов.

Сохраните следующий сборник схем как `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

- В первом разделе в `tasks` определяется группа ресурсов `myResourceGroup` в расположении `eastus`.
- Во втором разделе в `tasks` определяется кластер AKS `myAKSCluster` в группе ресурсов `myResourceGroup`.
- Для заполнителя `your_ssh_key` укажите свой открытый ключ RSA в однострочном формате, начиная с ssh-rsa.
- Для заполнителя `aks_version` примените команду [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions).

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook azure_create_aks.yml
```

После выполнения сборника схем отобразятся результаты, как показано ниже.

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Масштабирование узлов AKS

Сборник схем из предыдущего раздела определяет два узла. Измените число узлов, изменив значение `count` в блоке `agent_pool_profiles`.

Сохраните следующий сборник схем как `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

- Для заполнителя `your_ssh_key` укажите свой открытый ключ RSA в однострочном формате, начиная с ssh-rsa.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook azure_configure_aks.yml
```

После выполнения сборника схем отобразятся результаты, как показано ниже.

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Удаление управляемого кластера AKS

Пример сборника схем удаляет кластер AKS.

Сохраните следующий сборник схем как `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook azure_delete_aks.yml
```

После выполнения сборника схем отобразятся результаты, как показано ниже.

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Масштабирование приложения в службе Azure Kubernetes (AKS)](/azure/aks/tutorial-kubernetes-scale)