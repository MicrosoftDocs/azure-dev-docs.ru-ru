---
title: Руководство по настройке лаборатории в Azure DevTest Labs с помощью Ansible
description: Сведения о настройке лаборатории в Azure DevTest Labs с помощью Ansible
keywords: ansible, azure, devops, bash, playbook, devtest lab
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: b1b46c8ec92a25d33b810bafdabf8b66979e3c6d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682073"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Руководство по Настройка лаборатории в Azure DevTest Labs с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

С помощью [Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) разработчики могут автоматизировать создание сред виртуальных машин для своих приложений. Эти среды можно настраивать для разработки приложений, их тестирования и обучения. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание лаборатории
> * Установка политик лаборатории
> * Установка расписания лаборатории
> * Создание виртуальной сети лаборатории
> * Определение источника артефактов для лаборатории
> * Создание виртуальной машины в лаборатории
> * Список артефактов и источников артефактов лаборатории
> * Получение сведений об Azure Resource Manager для источников артефактов
> * Создание лабораторной среды
> * Создание лабораторного образа
> * Удаление лаборатории

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Создать группу ресурсов

Фрагмент кода из примера сборника схем создает группу ресурсов Azure. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Создание лаборатории

Следующая задача — создание образца лаборатории.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Установка политик лаборатории

Вы можете самостоятельно задать параметры политики лаборатории. Задать можно следующие значения:

- `user_owned_lab_vm_count` — количество виртуальных машин, которыми может владеть пользователь
- `user_owned_lab_premium_vm_count` — количество виртуальных машин уровня "Премиум", которыми может владеть пользователь
- `lab_vm_count` — максимальное количество виртуальных машин в лаборатории
- `lab_premium_vm_count` — максимальное количество виртуальных машин уровня "Премиум" в лаборатории
- `lab_vm_size` — допустимые размеры виртуальных машин
- `gallery_image` — допустимые образы коллекции
- `user_owned_lab_vm_count_in_subnet` — максимальное количество пользовательских виртуальных машин в подсети
- `lab_target_cost` — целевые затраты лаборатории

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Установка расписания лаборатории

В этом разделе расписание лаборатории настраивается с помощью образца задачи. 

Значение `lab_vms_startup`, приведенное в следующем фрагменте кода, используется для указания времени запуска виртуальной машины. Точно также значение параметра `lab_vms_shutdown` устанавливает время завершения работы виртуальной машины в лаборатории.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Создание виртуальной сети лаборатории

Эта задача по умолчанию создает виртуальную сеть лаборатории.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Определение источника артефактов для лаборатории

Источник артефактов является правильно структурированным репозиторием GitHub, содержащим определение артефакта и шаблоны Azure Resource Manager. Каждая лаборатория поставляется вместе с предварительно определенными общедоступными артефактами. Создание источника артефакта в лаборатории показано в следующих задачах.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Создание виртуальной машины в лаборатории

Создание в лаборатории виртуальной машины

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Список артефактов и источников артефактов лаборатории

Чтобы по умолчанию перечислить все пользовательские источники артефактов в лаборатории, используйте следующую задачу.

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Следующая задача отображает все артефакты.

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Получение сведений об Azure Resource Manager для источников артефактов

Чтобы посмотреть все шаблоны Azure Resource Manager в `public environment repository`, предопределите хранилище с помощью следующих шаблонов.

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Следующая задача получает сведения о конкретном шаблоне Azure Resource Manager из хранилища:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Создание лабораторной среды

Следующая задача создает лабораторную среду на основе одного из шаблонов из общедоступного репозитория сред.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Создание лабораторного образа

Образ виртуальной машины будет создан с помощью следующей задачи. С его помощью можно будет создавать идентичные виртуальные машины.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Удаление лаборатории

Чтобы удалить лабораторию, используйте следующую задачу:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Получение примера сборника схем

Существует два способа получения полного примера сборника схем:
- [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) и сохраните его в `devtestlab-create.yml`.
- Создайте файл с именем `devtestlab-create.yml` и скопируйте в него следующее содержимое:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Запуск сборника схем

Чтобы проверить различные возможности, описанные в этой статье, здесь необходимо выполнить сборник схем.

Перед выполнением сборника схем внесите следующие изменения.
- В разделе `vars` замените заполнитель `{{ resource_group_name }}` именем вашей группы ресурсов.
- Сохраните маркер GitHub в качестве переменной среды `GITHUB_ACCESS_TOKEN`.

Запустите сборник схем с помощью команды [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html).

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](/azure/ansible/)