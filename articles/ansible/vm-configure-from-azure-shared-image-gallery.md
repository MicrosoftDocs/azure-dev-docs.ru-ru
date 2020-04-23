---
title: Учебник. Создание виртуальной машины или масштабируемого набора виртуальной машины из Общей коллекции образов Azure с помощью Ansible
description: Узнайте, как с помощью Ansible создать виртуальную машину или масштабируемый набор виртуальных машин на основе универсального образа из Общей коллекции образов.
keywords: Ansible, Azure, DevOps, Bash, сборник схем, виртуальная машина, масштабируемый набор виртуальных машин, Общая коллекция образов
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: cd66899b5d5f37bc056d251a68117456062c16cf
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743265"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Руководство по созданию виртуальной машины или масштабируемого набора виртуальных машин из Общей коллекции образов Azure с помощью Ansible

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

[Общая коллекция образов](/azure/virtual-machines/windows/shared-image-galleries) — это служба, которая позволяет легко предоставлять общий доступ к управляемым пользователями образам, упорядочивать их а также управлять ими. Эту функцию целесообразно использовать в сценариях, где осуществляется обслуживание и публикация многих образов для совместного использования. Пользовательские образы могут совместно использоваться между разными подписками и клиентами Azure Active Directory. Образы можно также реплицировать в несколько регионов для более быстрого масштабирования развертываний.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание универсальной виртуальной машины и пользовательского образа.
> * Создание Общей коллекции образов.
> * Создание общего образа и версии образа.
> * Создание виртуальной машины с помощью универсального образа.
> * Создание масштабируемого набора виртуальных машин с помощью универсального образа.
> * Получение сведений об Общей коллекции образов, образе и версии.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Получение примера сборников схем

Существует два способа получения полного набора с примерами сборников схем:

- [Скачайте папку SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) и сохраните ее на локальном компьютере.
- Создайте файл для каждого раздела и скопируйте в него пример сборника схем.

Файл `vars.yml` содержит переменные, используемые всеми примерами сборника схем для этого учебника. Вы можете изменить файл, чтобы предоставить уникальные имена и значения.

Первый пример сборника схем `00-prerequisites.yml` создает все необходимое для работы с этим учебником:
- группу ресурсов, которая является логическим контейнером, где происходит развертывание ресурсов Azure и управление ими;
- виртуальную сеть, подсеть, общедоступный IP-адрес и сетевую карту для виртуальной машины;
- исходную виртуальную машину, используемую для создания универсального образа.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 00-prerequisites.yml
```

На [портале Azure](https://portal.azure.com) проверьте группу ресурсов, которую вы указали в `vars.yml`, чтобы просмотреть созданную виртуальную машину и различные ресурсы.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Подготовка виртуальной машины и создание пользовательского образа

Следующий сборник схем `01a-create-generalized-image.yml` обобщает исходную виртуальную машину, созданную на предыдущем шаге, а затем создает на ее основе пользовательский образ.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Проверьте группу ресурсов и убедитесь, что `testimagea` отображается.

## <a name="create-the-shared-image-gallery"></a>Создание Общей коллекции образов

Коллекция образов — это репозиторий для совместного использования образов и управления ими. Код из примера сборника схем в `02-create-shared-image-gallery.yml` создает Общую коллекцию образов в группе ресурсов.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Теперь в группе ресурсов можно увидеть новую коллекцию `myGallery`.

## <a name="create-a-shared-image-and-image-version"></a>Создание общего образа и версии образа.

Следующий сборник схем `03a-create-shared-image-generalized.yml` создает определение и версию образа.

Определение образа включает в себя: тип образа (Windows или Linux), заметки о выпуске, а также минимальные и максимальные требования к памяти. Версия образа — это версия этого образа. Коллекция, определение образа и версия образа помогают упорядочить образы в логические группы. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Теперь группа ресурсов содержит определение и версию образа для коллекции.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Создание виртуальной машины на основе универсального образа

Наконец, запустите `04a-create-vm-using-generalized-image.yml`, чтобы создать виртуальную машину на основе универсального образа, созданного на предыдущем шаге.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Создание масштабируемого набора виртуальных машин на основе универсального образа

Вы также можете создать масштабируемый набор виртуальных машин на основе универсального образа. Для этого запустите `05a-create-vmss-using-generalized-image.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Получение информации о коллекции

Чтобы получить сведения о коллекции, определении и версии образа, запустите `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Удаление общего образа

Чтобы удалить ресурсы коллекции, обратитесь к примеру сборника схем `07-delete-gallery.yml`. Удалите ресурсы в обратном порядке. Начните с удаления версии образа. После удаления всех версий образа вы можете удалить определение образа. Затем вы можете удалить коллекцию.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Код из примера сборника схем в этом разделе используется для следующего.

- Удаление двух групп ресурсов, созданных ранее.

Сохраните следующий сборник схем как `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Замените заполнитель `{{ resource_group_name }}` именем своей группы ресурсов.
- Будут удалены все ресурсы в указанных группах ресурсов.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](/azure/ansible/)