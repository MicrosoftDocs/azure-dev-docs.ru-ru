---
title: Руководство по настройке динамических списков ресурсов Azure с помощью Ansible
description: Узнайте, как управлять динамическими списками Azure с помощью Ansible
keywords: ansible, azure, разработка и операции, bash, cloudshell, динамические списки
ms.topic: tutorial
ms.date: 10/30/2020
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: ff23b6d4d363e8b83e33414c6518560fa82b8ee0
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395269"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Руководство по настройке динамических списков ресурсов Azure с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

Ansible можно использовать для извлечения информации из различных источников (включая облачные источники, такие как Azure) в *динамический список*. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Настройка двух тестовых виртуальных машин.
> * Добавление тега к одной из виртуальных машин.
> * Установка Nginx на виртуальные машины с тегами.
> * Настройка динамического списка, содержащего настроенные ресурсы Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Создание тестовых виртуальных машин

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте группу ресурсов Azure для хранения виртуальных машин, используемых в этом руководстве.

    > [!IMPORTANT]
    > Имя группы ресурсов Azure, создаваемой на этом этапе, должно содержать только строчные символы. В противном случае создание динамического списка завершится ошибкой.

    ```azurecli
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Создайте две виртуальные машины Linux в Azure одним из следующих способов:

    - **Скрипт playbook Ansible**. В статье [Создание базовой виртуальной машины в Azure с помощью Ansible](./vm-configure.md) показано, как создать виртуальную машину с помощью скрипта playbook Ansible. При использовании скрипта playbook для определения одной или нескольких виртуальных машин убедитесь, что вместо пароля используется SSH-подключение.

    - **Azure CLI**. Выполните каждую из следующих команд в Cloud Shell, чтобы создать две виртуальные машины:

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Добавление тега для виртуальной машины

Можно [использовать теги](/azure/azure-resource-manager/resource-group-using-tags#azure-cli) для организации ресурсов Azure по определенным пользователем категориям.

Выполните приведенную ниже команду [az resource tag](/cli/azure/resource#az-resource-tag), чтобы добавить тег для виртуальной машины `ansible-inventory-test-vm1` с ключом `Ansible=nginx`:

```azurecli
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

Замените `<YourAzureSubscriptionID>` своим идентификатором подписки.

## <a name="generate-a-dynamic-inventory"></a>Создание динамического списка

После определения виртуальных машин (и добавления тегов) необходимо создать динамический список.

Ansible предоставляет [подключаемый модуль динамической инвентаризации для Azure](https://github.com/ansible/ansible/blob/stable-2.9/lib/ansible/plugins/inventory/azure_rm.py). Инструкции по использованию подключаемого модуля приведены ниже.

1. Имя динамической инвентаризации должно заканчиваться на `azure_rm` и иметь расширение `yml` или `yaml`. В противном случае Ansible не будет обнаруживать нужный подключаемый модуль инвентаризации. Для примера этого учебника сохраните следующий сборник как `myazure_rm.yml`.

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Выполните следующую команду, чтобы проверить связь с виртуальными машинами в группе ресурсов.

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. По умолчанию проверка ключа узла включена, что может привести к следующей ошибке.

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```

    Отключите проверку ключа узла, задав для переменной среды `ANSIBLE_HOST_KEY_CHECKING` значение `False`.

    ```bash
    export ANSIBLE_HOST_KEY_CHECKING=False
    ```

1. После запуска сборника схем отобразятся результаты, как показано ниже.
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Включение тега виртуальной машины

- Выполните команду `ansible-inventory -i myazure_rm.yml --graph`, чтобы получить следующие выходные данные:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Вы также можете проверить подключение к виртуальной машине Nginx с помощью следующей команды:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Настройка Nginx на виртуальной машине с тегом

Теги позволяют просто и быстро работать с подгруппами виртуальных машин. Предположим, вам необходимо установить Nginx только на виртуальных машинах, которым присвоен тег `nginx`. Ниже показано, как просто это можно сделать.

1. Создайте файл `nginx.yml`.

   ```console
   code nginx.yml
   ```

1. Вставьте следующий пример кода в редактор.

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Сохраните файл и закройте редактор.

1. Запустите сборник схем с помощью команды [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html).

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Тестирование установки Nginx

В этом разделе показано, как проверить Nginx, установленный на виртуальной машине.

1. Извлеките IP-адрес виртуальной машины `ansible-inventory-test-vm1`, используя команду [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses). Возвращаемое значение (IP-адрес виртуальной машины) затем используется как параметр команды SSH, при помощи которого устанавливается подключение к виртуальной машине.

    ```azurecli
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Подключившись к виртуальной машине `ansible-inventory-test-vm1`, выполните команду [nginx -v](https://nginx.org/en/docs/switches.html), чтобы узнать, установлен ли веб-сервер Nginx.

    ```console
    nginx -v
    ```

1. После выполнения команды `nginx -v` отобразится версия Nginx (во второй строке). Это обозначает, что Nginx установлен.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Нажмите клавиши `<Ctrl>D`, чтобы отключить сеанс SSH.

1. При выполнении предыдущих шагов для виртуальной машины `ansible-inventory-test-vm2` отобразится информационное сообщение о том, где можно получить Nginx (это означает, что этот компонент еще не установлен).

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Краткое руководство. Настройка виртуальных машин Linux в Azure с помощью Ansible](./vm-configure.md)
