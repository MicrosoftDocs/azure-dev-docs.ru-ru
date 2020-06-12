---
title: Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Ansible
description: Из этой статьи вы узнаете, как использовать Ansible для настройки безопасности виртуальных машин с помощью Azure Key Vault.
keywords: ansible, azure, devops, key vault, безопасность, учетные данные, секреты, ключи, сертификаты, модули ansible для azure, группа ресурсов, azure_rm_resourcegroup,
ms.topic: tutorial
ms.date: 04/20/2020
ms.openlocfilehash: 84ed514e742c8a8fa3a9acc9328fc71743dfc5cb
ms.sourcegitcommit: 79890367158a9931909f11da1c894daa11188cba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84145972"
---
# <a name="tutorial-use-azure-key-vault-with-a-linux-virtual-machine-in-ansible"></a>Руководство по Использование Azure Key Vault с виртуальной машиной Linux в Ansible

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

В этом руководстве описывается, как использовать коллекцию Ansible для модулей Azure при работе с [Azure Key Vault](/azure/key-vault/general/overview). Azure Key Vault позволяет централизованно хранить учетные данные, такие как секреты, ключи и сертификаты приложений. Разделение учетных данных и кода приложения повышает безопасность системы. В рамках такого подхода также намного проще реализовать шаблон управления со сменой учетной данных, срок действия которых устанавливается автоматически.

> [!div class="checklist"]
>
> * Использование Azure CLI для получения значений подписки и субъекта-службы Azure.
> * Хранение значений ключей в виде переменных среды Linux.
> * Получение переменных среды Linux из сборниках схем Ansible.
> * Создайте хранилище ключей.
> * Настройка политики доступа для хранилища ключей.
> * Добавление политики доступа в хранилище ключей с помощью портала Azure.
> * Создание секрета хранилища ключей
> * Использование модуля оболочки Ansible для получения секрета хранилища ключей.
> * Создание виртуальной машины вместе со всеми составными компонентами.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
[!INCLUDE [ansible-configure-azure-collection.md](includes/ansible-configure-azure-collection.md)]
    
## <a name="get-azure-subscription-info"></a>Получение сведений о подписке Azure

Воспользуйтесь Azure CLI, чтобы получить сведения о подписке Azure, необходимые при использовании модулей Ansible для Azure. 

1. Получите идентификатор подписки Azure и идентификатор клиента подписки Azure с помощью команды `az account show`. Вместо заполнителя `<Subscription>` укажите имя или идентификатор подписки Azure. Эта команда отобразит множество значений ключей, связанных с подпиской Azure по умолчанию. Если у вас несколько подписок, вы можете указать текущую подписку с помощью команды [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set). Из предоставленных выходных данных команды запишите значения **ID** и **tenantID**.

    ```azurecli
    az account show --subscription "<Subscription>" --query tenantId
    ```

1. Если у вас нет субъекта-службы для подписки Azure, [создайте субъект-службу Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Из выходных данных команды запишите значение **appId**.

1. Получите идентификатор объекта субъекта-службы с помощью команды `az ad sp show`. Вместо заполнителя `<ApplicationID>` укажите appId субъекта-службы. Параметр `--query` указывает, какое значение необходимо выводить в *stdout*. В нашем примере это идентификатор объекта субъекта-службы.

    ```azurecli
    az ad sp show --id <ApplicationID> --query objectId
    ```
    
1. Сохраните следующие значения переменных среды: идентификатор подписки Azure, идентификатор клиента в подписке Azure и идентификатор объекта субъекта-службы. Способ выполнения сборника схем, место хранения значений подписки и учетных данных, а также способ получения этих значений зависят от конкретной среды. В этой демонстрации мы будем использовать Azure Cloud Shell с хранением нужных значений Azure в `~/.bashrc` путем добавления следующих срок в конец файла:

    ```bash
    export AZ_SUBSCRIPTION_ID="<subscriptionID>"
    export AZ_TENANT_ID="<tenantID>"
    export AZ_OBJECT_ID="<objectID>"
    export AZ_CLIENT_ID="<appID>"
    ```

## <a name="declare-the-azure-collection"></a>Объявление коллекции Azure

После [скачивания последней версии коллекции Azure](#prerequisites) определите ее использование с помощью ключа `collections`.

```yml
- hosts: all
  collections:
    - azure.azcollection
```

## <a name="create-azure-resource-group-for-the-key-vault"></a>Создание группы ресурсов Azure для хранилища ключей

Следующий фрагмент сборниках схем создает группу ресурсов с уникальным именем, в которой будет создано хранилище ключей. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"

    - name: Create a resource group to hold the key vault
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"
```

**Примечания.**

- В этой демонстрации хранилище ключей создается в качестве единственного ресурса в группе ресурсов. Как правило, хранилище ключей изолируется от ресурсов, которые его используют. Такой подход помогает предотвратить случайное удаление хранилища ключей при удалении других ресурсов.
- Так как имя хранилища ключей должно быть уникальным в пределах Azure, в этой демонстрации мы создадим случайное значение *postfix*. Это значение добавляется к имени группы ресурсов хранилища ключей и хранилища ключей (которое вы создадите с помощью инструкций из следующего раздела). Код в задаче `Prepare random postfix` создает случайное значение postfix, которое назначается переменной `rpfx`.
- В задаче `Set facts` используется команда `lookup` для получения идентификатора подписки Azure, который хранится в виде переменной среды.
- [Модуль azure_rm_resourcegroup](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html) позволяет создать новую группу ресурсов.
- Задача `debug` в конце сборника схем выводит имя новой группы ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Как упоминалось в предыдущем разделе, имя хранилища ключей должно быть уникальным в пределах Azure. Поэтому следующий фрагмент сборника схем присваивает переменной `kv` результат объединения литерала `kv` и значения `rpfx`.

```yml
vars:
  kv: "kv{{ rpfx }}"
  kv_rg: "kv_rg_{{ rpfx }}"

tasks:
  - name: Set facts
    set_fact:
      az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
      az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"

  - name: Create a key vault
    azure_rm_keyvault:
      subscription_id: "{{ az_sub_id }}"
      resource_group: "{{ kv_rg }}"
      vault_name: "{{ kv }}"
      vault_tenant: "{{ az_tenant_id }}"
      enabled_for_deployment: yes
      sku:
        name: standard
        family: A
      access_policies:
        - object_id: "{{ az_object_id }}"
          tenant_id: "{{ az_tenant_id }}"
          secrets:
            - get
            - list
            - set
  - debug:
      msg: "New key vault name = {{ kv }} within the {{ kv_rg }} resource group"

```

**Примечания.**

- [Модуль azure_rm_keyvault](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvault_module.html) позволяет создать хранилище ключей.
- При создании политики доступа для хранилища ключей были указаны идентификатор объекта и идентификатор клиента. Эти значения определяют политику доступа для конкретного субъекта-службы (который связан с подпиской Azure). Но при переходе на портал Azure и попытке просмотра секретов хранилища ключей могут отобразиться сообщения об ошибке. Примеры таких сообщений: "Операция List не включена в этой политике доступа хранилища ключей" и "У вас нет разрешений на просмотр этого содержимого". Эти сообщения означают, что у вас как пользователя Active Directory нет нужных прав доступа. В следующем разделе показано, как добавить политику доступа для своей учетной записи в хранилище ключей.
- Задача `debug` в конце сборника схем выводит имя нового хранилища ключей.

## <a name="add-yourself-to-key-vault-access-policy"></a>Добавление собственной учетной записи в политику доступа для хранилища ключей

Если вы хотите просмотреть секреты хранилища ключей или выполняете шаги из демонстрации, добавьте политику доступа для своего идентификатора Azure Active Directory. Ниже описано, как добавить в хранилище ключей политику доступа для своей учетной записи пользователя.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. В основном поле поиска на странице введите `key vaults` и в разделе **Службы** выберите **Хранилища ключей**.

1. Выберите хранилище ключей, созданное с помощью инструкций из предыдущего раздела (его имя было выведено в stdout из сборника схем).

1. Выберите **Политики доступа**.

1. Отобразится отдельная политика доступа с вашим идентификатором Azure Active Directory, который представляет указанный субъект-службу.

1. Нажмите **Добавить политику доступа**.

1. Нажмите **Выбрать субъект**.

1. На вкладке **Субъект** введите свой адрес электронной почты в поле поиска.

1. Выберите в отфильтрованном списке соответствующую запись.

1. Сведения о выбранном пользователе будут скопированы в список **Выбранный участник**. Щелкните **Выбрать**.

1. Выберите соответствующие параметры в разделах **Разрешения ключей**, **Разрешения секретов** и **Разрешения сертификатов**. Для этой демонстрации достаточно выбрать **Разрешения секретов** и указать операции **Get**, **List** и **Set**.

1. Выберите **Добавить**.

1. Теперь новая политика доступа для выбранного пользователя отображается на странице **Политики доступа**.

1. Щелкните **Сохранить**.

1. Выберите **Уведомления** в правом верхнем углу страницы портала. Прежде чем переходить к следующему шагу, дождитесь обновления политики доступа.

## <a name="create-a-key-vault-secret"></a>Создание секрета хранилища ключей

В следующем фрагменте сборника схем Ansible показано, как создать секрет хранилища ключей:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

  tasks:
    - name: Set facts
      set_fact:
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"
```

**Примечания.**

- [Модуль azure_rm_keyvaultsecret](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvaultsecret_module.html) создает секрет хранилища ключей.
- Для простоты демонстрация включает значения `secret_name` и `secret_value`. Но сборники схем представляют собой файлы формата "инфраструктура как код", как и любой исходный код вашего проекта. Поэтому в рабочих средах такие значения не должны храниться в файлах с открытым текстом.
- После выполнения этого кода на вкладке **Секреты** для хранилища ключей отобразится добавленный секрет с именем `testsecret`. Чтобы просмотреть его, выберите секрет, укажите текущую версию и щелкните **Показать значение секрета**.

## <a name="get-a-key-vault-secret"></a>Получение секрета хранилища ключей

В следующем фрагменте сборника схем Ansible показано, как получить последнюю версию секрета для хранилища ключей:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

tasks:
    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']
```

**Примечания.**

- **Модуль azure_rm_keyvaultsecret_info** возвращает секрет хранилища ключей. Этот модуль доступен только при использовании коллекции Ansible для модулей Azure. 
- Если при выполнении этого фрагмента появляется сообщение об ошибке, убедитесь, что выполнены все инструкции из раздела [Предварительные требования](#prerequisites).
- Для простоты демонстрация включает значения `secret_name` и `secret_value`. Но сборники схем представляют собой файлы формата "инфраструктура как код", как и любой исходный код вашего проекта. Поэтому в рабочих средах такие значения не должны храниться в файлах с открытым текстом.

## <a name="run-the-complete-playbook"></a>Выполнение готового сборника схем

После создания хранилища ключей и его секрета эти сведения можно использовать для защиты ресурсов Azure, например виртуальных машин. Приведенный ниже сборник схем Ansible выполняет задачи, приведенные в этом руководстве, и создает готовую виртуальную машину. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  collections:
    - azure.azcollection
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus
    kv: "kv{{ rpfx }}"
    kv_uri: "https://{{ kv }}.vault.azure.net"
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

    # Test VM vars
    test_vm_rg: kv_test_vm_rg
    test_vm_rg_loc: eastus
    test_vm: kvtestvm
    test_vm_vnet: "kv_test_vm_vnet"
    test_vm_subnet: kv_test_vm_subnet
    test_vm_public_ip: kv_test_vm_public_ip
    test_vm_nsg: kv_test_vm_nsg
    test_vm_nsg_list: 
      - name: Allow-SSH
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 300
        port: 22 
        source_address_prefix: Internet
      - name: Allow-HTTP
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 100
        port: 80
        source_address_prefix: Internet 
    test_vm_nic: kv_test_vnic
    admin_username: testadmin

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"
        az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
        az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a resource group to hold the Key Vault instance
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"

    - name: Create instance of Key Vault
      azure_rm_keyvault:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ kv_rg }}"
        vault_name: "{{ kv }}"
        vault_tenant: "{{ az_tenant_id }}"
        enabled_for_deployment: yes
        sku:
          name: standard
          family: A
        access_policies:
          - object_id: "{{ az_object_id }}"
            tenant_id: "{{ az_tenant_id }}"
            secrets:
              - get
              - list
              - set

    - debug:
        msg: "New Key Vault instance name = {{ kv }} within the {{ kv_rg }} resource group"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"

    - name: Register Key Vault provider.
      shell:
        az provider register -n Microsoft.KeyVault

    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']

    - name: Create resource group for test VM.
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ test_vm_rg }}"
        location: "{{ test_vm_rg_loc }}"

    - name: Create virtual network.
      azure_rm_virtualnetwork:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_vnet }}"
        address_prefixes: "172.16.0.0/16"

    - name: Create subset within virtual network.
      azure_rm_subnet:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        virtual_network_name: "{{ test_vm_vnet }}"
        name: "{{ test_vm_subnet }}"
        address_prefix_cidr:  "172.16.10.0/24"

    - name: Create public IP address.
      azure_rm_publicipaddress:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        allocation_method: Static
        name: "{{ test_vm_public_ip }}"

    - name: Create Network Security Group and rules.
      azure_rm_securitygroup:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nsg}}"
        rules:
          - name: "{{ item.name }}"
            access: "{{ item.access }}"
            protocol: "{{ item.protocol }}"
            direction: "{{ item.direction }}"
            destination_port_range: "{{ item.port }}"
            priority: "{{ item.priority }}"
            source_address_prefix: "{{ item.source_address_prefix }}"
      loop: "{{ test_vm_nsg_list }}"

    - name: Create virtual network interface card (NIC).
      azure_rm_networkinterface:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nic }}"
        virtual_network: "{{ test_vm_vnet }}"
        subnet: "{{ test_vm_subnet }}"
        ip_configurations:
          - name: ipconfig
            public_ip_address_name: "{{ test_vm_public_ip }}"
            primary: yes
        security_group: "{{ test_vm_nsg }}"

    - name: Create virtual machine.
      azure_rm_virtualmachine:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm }}"
        admin_username: " {{ admin_username }} "
        admin_password: " {{ output['secrets'][0]['secret'] }}"
        vm_size: Standard_B1ms
        network_interfaces: "{{ test_vm_nic }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest

```

**Примечания.**

- В качестве пароля *администратора* для этой виртуальной машины задан секрет хранилища ключей.
- Возможность полного выполнения сборника схем зависит от вашей тестовой среды. Возможно, перед созданием ключа вам потребуется вручную добавить свою учетную запись в политику доступа к хранилищу ключей. Эта задача описана в разделах [Создание хранилища ключей](#create-a-key-vault) и [Добавление собственной учетной записи в политику доступа к хранилищу ключей](#add-yourself-to-key-vault-access-policy).
- Как видите, для создания виртуальных машин Azure и их составных компонентов можно использовать разные модули Ansible. Дополнительные сведения о различных модулях Ansible, которые используются для создания виртуальных машин, см. в следующих статьях:
    - [Модуль группы ресурсов Azure (azure_rm_resourcegroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html)
    - [Модуль виртуальной сети Azure (azure_rm_virtualnetwork)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualnetwork_module.html)
    - [Модуль подсети виртуальной сети Azure (azure_rm_subnet)](https://docs.ansible.com/ansible/latest/modules/azure_rm_subnet_module.html)
    - [Модуль общедоступного IP-адреса Azure (azure_rm_publicipaddress)](https://docs.ansible.com/ansible/latest/modules/azure_rm_publicipaddress_module.html)
    - [Модуль группы безопасности сети Azure (azure_rm_securitygroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_securitygroup_module.html)
    - [Сетевой интерфейс Azure (azure_rm_networkinterface)](https://docs.ansible.com/ansible/latest/modules/azure_rm_networkinterface_module.html)
    - [Виртуальная машина Azure (azure_rm_virtualmachine)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualmachine_module.html)
    
## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. Вместо заполнителя `<kv_rg>` укажите группу ресурсов, в которой хранится демонстрационное хранилище ключей.

```yml
- hosts: localhost
  vars:
    kv_rg: <kv_rg>
    test_vm_rg: kv_test_vm_rg
  tasks:
    - name: Delete the key vault resource group
      azure_rm_resourcegroup:
        name: "{{ kv_rg }}"
        force_delete_nonempty: yes
        state: absent
    - name: Delete the test vm resource group
      azure_rm_resourcegroup:
        name: "{{ test_vm_rg }}"
        force_delete_nonempty: yes
        state: absent
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Обзор системы безопасности Azure Key Vault](/azure/key-vault/general/overview-security)
