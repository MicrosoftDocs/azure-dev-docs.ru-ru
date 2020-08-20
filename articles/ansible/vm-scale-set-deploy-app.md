---
title: Руководство по развертыванию приложений в масштабируемых наборах виртуальных машин в Azure c помощью Ansible
description: Узнайте, как с помощью Ansible настроить масштабируемые наборы виртуальных машин Azure и развернуть приложение в масштабируемом наборе.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 161592c9f437de0f72204051b222c7ed8c5f99e0
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240846"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Руководство по развертыванию приложений в масштабируемых наборах виртуальных машин в Azure c помощью Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Получение сведений об узле для группы виртуальных машин Azure
> * Клонирование и сборка примера приложения
> * Установка JRE (среды выполнения Java) в масштабируемом наборе
> * Развертывание приложения Java в масштабируемом наборе

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) используется для скачивания примеров Java, которые есть в этом руководстве.
- **Пакет SDK для Java SE (JDK)** . [JDK](https://aka.ms/azure-jdks) используется для сборки примера проекта Java.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) используется для сборки примера проекта Java.

## <a name="get-host-information"></a>Получение сведений об узле

Код из сборника схем в этом разделе извлекает сведения об узле для группы виртуальных машин. Этот код получает общедоступные IP-адреса и подсистему балансировки нагрузки в указанной группе ресурсов и создает группу узлов `scalesethosts` в списке ресурсов.

Сохраните следующий пример сборника схем как `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Подготовка приложения для развертывания

Код из сборника схем в этом разделе использует `git` для клонирования примера проекта Java из GitHub и выполняет сборку этого проекта. 

Сохраните следующий сборник схем как `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Запустите пример сборника схем Ansible, выполнив следующую команду:

  ```bash
  ansible-playbook app.yml
  ```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Развертывание приложения в масштабируемом наборе

Код из сборника схем в этом разделе используется для следующего.

* Установите JRE в группе узлов `saclesethosts`.
* Разверните приложение Java в группе узлов `saclesethosts`.

Существуют два способа получить пример сборника схем.

* [Скачайте сборник схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) и сохраните его в `vmss-setup-deploy.yml`.
* Создайте файл с именем `vmss-setup-deploy.yml` и скопируйте в него следующее содержимое.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* В разделе `vars` замените заполнитель `{{ admin_password }}` собственным паролем.
* Чтобы использовать тип соединения SSH с паролями, установите программу sshpass.

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* В некоторых средах может произойти ошибка использования пароля SSH вместо ключа. Если вы видите эту ошибку, можно отключить проверку ключа узла, добавив следующую строку в файл `/etc/ansible/ansible.cfg` или `~/.ansible.cfg`.

    ```bash
    [defaults]
    host_key_checking = False
    ```

Выполните следующую команду, чтобы запустить сборник схем:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Результат выполнения команды ansible-playbook указывает, что пример приложения Java установлен в группу узлов масштабируемого набора.

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Проверка результатов

Проверьте результаты своей работы, перейдя по URL-адресу подсистемы балансировки нагрузки для масштабируемого набора.

![Приложение Java работает в масштабируемом наборе в Azure.](media/vm-scale-set-deploy-app/demo.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. автомасштабированию масштабируемых наборов виртуальных машин в Azure с помощью Ansible](./vm-scale-set-auto-scale.md)