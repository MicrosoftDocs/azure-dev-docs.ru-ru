---
title: Руководство по настройке динамических списков ресурсов Azure с помощью Ansible
description: Узнайте, как управлять динамическими списками Azure с помощью Ansible
keywords: ansible, azure, разработка и операции, bash, cloudshell, динамические списки
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 31966012fb5178fe76a6eadfbd9830a5cc17ebe2
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743388"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Руководство по настройке динамических списков ресурсов Azure с помощью Ansible

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

1. Откройте [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Создайте группу ресурсов Azure для хранения виртуальных машин, используемых в этом руководстве.

    > [!IMPORTANT]    
    > Имя группы ресурсов Azure, создаваемой на этом этапе, должно содержать только строчные символы. В противном случае создание динамического списка завершится ошибкой.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Создайте две виртуальные машины Linux в Azure одним из следующих способов:

    - **Скрипт playbook Ansible**. В статье [Создание базовой виртуальной машины в Azure с помощью Ansible](./vm-configure.md) показано, как создать виртуальную машину с помощью скрипта playbook Ansible. При использовании скрипта playbook для определения одной или нескольких виртуальных машин убедитесь, что вместо пароля используется SSH-подключение.

    - **Azure CLI**. Выполните каждую из следующих команд в Cloud Shell, чтобы создать две виртуальные машины:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Добавление тега для виртуальной машины

Можно [использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) для организации ресурсов Azure по определенным пользователем категориям. 

### <a name="using-ansible-version--28"></a>Использование версий Ansible, предшествующих версии 2.8
Выполните приведенную ниже команду [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), чтобы добавить тег для виртуальной машины `ansible-inventory-test-vm1` с ключом `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Если вы используете Ansible версии 2.8 и выше
Выполните приведенную ниже команду [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), чтобы добавить тег для виртуальной машины `ansible-inventory-test-vm1` с ключом `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Создание динамического списка

После определения виртуальных машин (и добавления тегов) необходимо создать динамический список.

### <a name="using-ansible-version--28"></a>Использование версий Ansible, предшествующих версии 2.8

Ansible предоставляет сценарий Python [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py), который создает динамический список ресурсов Azure. Ниже описано, как использовать скрипт `azure_rm.py` для подключения двух тестовых виртуальных машин Azure:

1. Получите скрипт `azure_rm.py`, используя команду `wget` GNU:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Измените разрешения на доступ к скрипту `azure_rm.py`, используя команду `chmod`: В следующей команде используется параметр `+x`, чтобы разрешить выполнение указанного файла (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Подключитесь к своей группе ресурсов, используя [команду Ansible](https://docs.ansible.com/ansible/2.4/ansible.html): 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Когда подключение будет установлено, отобразятся результаты, аналогичные приведенным ниже выходным данным:

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Использование Ansible 2.8 и более поздних версий

Начиная с версии 2.8, Ansible предоставляет [подключаемый модуль динамической инвентаризации для Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Инструкции по использованию подключаемого модуля приведены ниже.

1. Для этого подключаемого модуля инвентаризации требуется файл конфигурации. В конце файл конфигурации должен содержать `azure_rm`. Кроме того, он должен использовать расширение `yml` или `yaml`. Для примера этого учебника сохраните следующий сборник как `myazure_rm.yml`.

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

1. При выполнении предыдущей команды может произойти приведенная ниже ошибка.

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Если произошла ошибка "проверки ключа узла", добавьте следующую строку в файл конфигурации Ansible. Файл конфигурации Ansible расположен здесь: `/etc/ansible/ansible.cfg` или `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. После запуска сборника схем отобразятся результаты, как показано ниже.
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Включение тега виртуальной машины

### <a name="if-youre-using-ansible--28"></a>Если вы используете Ansible версии ниже 2.8

- После установки тег нужно "включить". Один из способов включения тега — экспортировать его в переменную среды `AZURE_TAGS` с помощью команды `export`.

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Выполните следующую команду:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Теперь отображается только одна виртуальная машина (тег которой совпадает со значением, экспортированным в переменную среды `AZURE_TAGS`).

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Если вы используете Ansible версии 2.8 и выше

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

1. Запустите сборник схем с помощью команды `ansible-playbook`.

   - Версия Ansible, предшествующая версии 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible 2.8 или более поздняя версия:

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

1. Извлеките IP-адрес виртуальной машины `ansible-inventory-test-vm1`, используя команду [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses). Возвращаемое значение (IP-адрес виртуальной машины) затем используется как параметр команды SSH, при помощи которого устанавливается подключение к виртуальной машине.

    ```azurecli-interactive
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Настройка виртуальных машин Linux в Azure с помощью Ansible](./vm-configure.md)