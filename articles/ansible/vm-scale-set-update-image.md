---
title: Руководство по обновлению пользовательского образа масштабируемого набора виртуальных машин Azure с помощью Ansible
description: Использование Ansible для обновления масштабируемого набора виртуальных машин в Azure с помощью пользовательского образа
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: a80df95b0977a4c93fc91a188ce4b69afda80169
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740962"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Руководство по обновлению пользовательского образа масштабируемого набора виртуальных машин Azure с помощью Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../../includes/open-source-devops-intro-vm-scale-set.md)]

После развертывания виртуальной машины вы настраиваете ее с помощью программного обеспечения, необходимого для вашего приложения. Вместо того, чтобы настраивать каждую виртуальную машину, можно создать пользовательский образ. Пользовательский образ — это моментальный снимок существующей виртуальной машины, который содержит любое установленное программное обеспечение. При [настройке масштабируемого набора](./vm-scale-set-configure.md) вы указываете образ, используемый для этого масштабируемого набора виртуальных машин. Используя пользовательский образ, каждый экземпляр виртуальной машины настраивается для вашего приложения одинаково. В некоторых случаях может потребоваться обновление пользовательского образа масштабируемого набора. Эта задача является ключевой в этом руководстве.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Настройка двух виртуальных машин с помощью HTTPD
> * Создание пользовательского образа на основе существующей виртуальной машины
> * Создание масштабируемого набора на основе образа
> * Обновление пользовательского образа

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Настройка двух виртуальных машин

В этом разделе с помощью кода сборника схем создаются две виртуальные машины с установленным HTTPD. 

Страница `index.html` для каждой виртуальной машины отображает тестовую строку.

* Для первой виртуальной машины отображается значение `Image A`
* Для второй виртуальной машины отображается значение `Image B`

Эта строка предназначена для имитации настройки каждой виртуальной машины с помощью различного программного обеспечения.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) и сохраните его в `create_vms.yml`.
* Создайте файл с именем `create_vms.yml` и скопируйте в него следующее содержимое.

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`, заменив `myrg` именем группы ресурсов.

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Из-за разделов `debug` сборника схем команда `ansible-playbook` напечатает IP-адрес на каждой виртуальной машине. Скопируйте эти IP-адреса для последующего использования.

![IP-адреса виртуальной машины](media/vm-scale-set-update-image/ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Подключение к двум виртуальным машинам

В этом разделе рассматривается подключение к виртуальной машине. Как упоминалось в предыдущем разделе, строки `Image A` и `Image B` имитируют наличие двух отдельных виртуальных машин с различными конфигурациями.

Используя IP-адреса из предыдущего раздела, подключитесь к обеим виртуальным машинам.

![Снимок экрана из виртуальной машины A](media/vm-scale-set-update-image/vm-a.png)

![Снимок экрана из виртуальной машины В](media/vm-scale-set-update-image/vm-b.png)

## <a name="create-images-from-each-vm"></a>Создание образов на основе каждой виртуальной машины

На этом этапе у вас есть две виртуальные машины со слегка отличающимися конфигурациями (их файлами `index.html`).

Код сборника схем в этом разделе пользовательский образ для каждой виртуальной машины.

* `image_vmforimageA`. Пользовательский образ, созданный для виртуальной машины, который демонстрирует `Image A` на домашней странице.
* `image_vmforimageB`. Пользовательский образ, созданный для виртуальной машины, который демонстрирует `Image B` на домашней странице.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) и сохраните его в `capture-images.yml`.
* Создайте файл с именем `capture-images.yml` и скопируйте в него следующее содержимое.

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Запустите сборник схем с помощью команды `ansible-playbook`, заменив `myrg` именем группы ресурсов.

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Создание масштабируемого набора с помощью образа A

В этом разделе используется сборник схем для настройки следующих ресурсов Azure.

* Общедоступный IP-адрес
* Подсистема балансировки нагрузки
* Масштабируемый набор, который ссылается на `image_vmforimageA`

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) и сохраните его в `create-vmss.yml`.
* Создайте файл с именем `create-vmss.yml` и скопируйте в него следующее:

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`, заменив `myrg` именем группы ресурсов.

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Благодаря разделу `debug` сборника схем команда `ansible-playbook` напечатает IP-адрес масштабируемого набора. Скопируйте этот IP-адрес для последующего использования.

![Общедоступный IP-адрес](media/vm-scale-set-update-image/public-ip.png)

## <a name="connect-to-the-scale-set"></a>Подключение к масштабируемому набору

В этом разделе вы научитесь подключаться к масштабируемому набору. 

Используя IP-адрес из предыдущего раздела, подключитесь к масштабируемому набору.

Как упоминалось в предыдущем разделе, строки `Image A` и `Image B` имитируют наличие двух отдельных виртуальных машин с различными конфигурациями.

Масштабируемый набор ссылается на пользовательский образ, который имеет имя `image_vmforimageA`. Пользовательский образ `image_vmforimageA` был создан на основе виртуальной машины, чья домашняя страница отображает `Image A`.

Таким образом вы увидите домашнюю страницу с `Image A`.

![Масштабируемый набор связан с первой виртуальной машиной](media/vm-scale-set-update-image/initial-vm-scale-set.png)

Перейдя к следующему разделу, не закрывайте окно браузера.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Изменение пользовательского образа в масштабируемом наборе и обновление экземпляров

В этом разделе код сборника схем изменяет образ масштабируемого набора — с `image_vmforimageA` на `image_vmforimageB`. Кроме того обновляются все текущие виртуальные машины, развернутые в масштабируемом наборе.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) и сохраните его в `update-vmss-image.yml`.
* Создайте файл с именем `update-vmss-image.yml` и скопируйте в него следующее содержимое.

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Запустите сборник схем с помощью команды `ansible-playbook`, заменив `myrg` именем группы ресурсов.

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Затем вернитесь в браузер и обновите страницу. 

Вы увидите, что базовый пользовательский образ виртуальной машины обновлен.

![Масштабируемый набор связан со второй виртуальной машиной](media/vm-scale-set-update-image/updated-vm-scale-set.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий код как `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](/azure/ansible)