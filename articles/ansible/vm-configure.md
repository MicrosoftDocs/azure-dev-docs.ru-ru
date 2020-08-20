---
title: Краткое руководство. Настройка виртуальных машин Linux в Azure с помощью Ansible
description: Из этого краткого руководства вы узнаете, как с помощью Ansible создать виртуальную машину Linux в Azure.
keywords: ansible, azure, devops, virtual machine
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: c9f6d9a954b8bdbd05ede42cc69a0078eae3cea2
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240436"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Краткое руководство. Настройка виртуальных машин Linux в Azure с помощью Ansible

Используя декларативный язык, Ansible позволяет автоматизировать создание, конфигурацию и развертывание ресурсов Azure с помощью *сборников схем*. Эта статья содержит пример сборника схем Ansible для настройки виртуальных машин Linux. [Полный сборник схем Ansible](#complete-sample-ansible-playbook) указан в конце этой статьи.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для Ansible требуется группа ресурсов, в которой будут развертываться все ресурсы. В следующем разделе примера сборника схем Ansible описывается создание группы ресурсов `myResourceGroup` в расположении `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Создание виртуальной сети

При создании виртуальной машины Azure вам потребуется [виртуальная сеть](/azure/virtual-network/virtual-networks-overview). Вы можете создать ее или использовать уже готовую. Кроме того, необходимо решить, как будет предоставляться доступ к виртуальным машинам по виртуальной сети. В следующем разделе примера сборника схем Ansible описывается создание виртуальной сети `myVnet` в адресном пространстве `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Все ресурсы Azure, развернутые в виртуальной сети, также развертываются в [подсети](/azure/virtual-network/virtual-network-manage-subnet) этой виртуальной сети. 

В следующем разделе примера сборника схем Ansible описывается создание подсети `mySubnet` в виртуальной сети `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса





[Общедоступные IP-адреса](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) позволяют устанавливать входящее подключение от интернет-ресурсов к ресурсам Azure. Также они позволяют устанавливать исходящее подключение от ресурсов Azure к общедоступным службам Azure. В обоих случаях используемому ресурсу назначается IP-адрес. Этот адрес остается выделенным ресурсу, пока вы не отмените его назначение. Если ресурсу не назначен общедоступный IP-адрес, он все равно сможет устанавливать исходящие подключения к Интернету. Azure устанавливает подключение и динамически назначает доступный IP-адрес. Динамически назначенный адрес не является выделенным адресом ресурса.

В следующем разделе примера сборника схем Ansible описывается создание общедоступного IP-адреса `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети

[Группы безопасности сети](/azure/virtual-network/security-overview) позволяют фильтровать трафик, передаваемый между ресурсами Azure в виртуальной сети. Определены правила безопасности, которые управляют входящим и исходящим трафиком ресурсов Azure. Дополнительные сведения о ресурсах Azure и группах безопасности сети см. в разделе [Интеграция виртуальной сети для служб Azure](/azure/virtual-network/virtual-network-for-azure-services).

Следующий сборник схем создает группу безопасности сети `myNetworkSecurityGroup`. Она содержит правило, которое разрешает передачу трафика SSH через TCP-порт 22.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Создание виртуального сетевого интерфейса

Виртуальный сетевой интерфейс позволяет виртуальной машине подключаться к определенной виртуальной сети, общедоступному IP-адресу и группе безопасности сети. 

В следующем разделе примера сборника схем Ansible описывается создание виртуального сетевого интерфейса `myNIC`, подключенного к созданным ресурсам виртуальной сети.

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Последний шаг — создание виртуальной машины, которая использует все ресурсы, созданные при работе с предыдущими разделами этой статьи. 

Пример сборника схем Ansible, представленный в этом разделе, позволяет создать виртуальную машину `myVM` и подключить карточку виртуального сетевого интерфейса `myNIC`. Замените заполнитель &lt;your-key-data> собственными полными данными общедоступного ключа.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Завершение примера сборника схем Ansible

В этом разделе указан весь пример сборника схем Ansible, созданный при работе с этой статьей. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network interface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Запуск примера сборника схем Ansible

В этом разделе рассматривается запуск примера сборника схем Ansible, представленного в этой статье.

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл (для хранения сборника схем) под названием `azure_create_complete_vm.yml` и откройте его в редакторе VI следующим образом:

   ```bash
   vi azure_create_complete_vm.yml
   ```

1. Нажмите ключ **I**, чтобы войти в режим вставки.

1. Вставьте [полный пример сборника схем Ansible](#complete-sample-ansible-playbook) в редактор.

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1. Запустите пример сборника схем Ansible.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Результат должен быть аналогичным приведенному ниже. Здесь видно, что виртуальная машина успешно создана:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. Команда SSH используется для доступа к виртуальной машине Linux. Замените заполнитель &lt;ip-address> IP-адресом из предыдущего шага.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Краткое руководство. Управление виртуальной машиной Linux в Azure с помощью Ansible](./vm-manage.md)
