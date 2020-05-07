---
title: Руководство по управлению веб-трафиком с помощью Шлюза приложений Azure и Ansible
description: Сведения об использовании службы Ansible для создания и настройки входящего веб-трафика Шлюза приложений Azure
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, web traffic
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 3ff9a43513a2ee34964e6e82447e3398469db8f8
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743721"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Руководство по управлению веб-трафиком с помощью Шлюза приложений Azure и Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[Шлюз приложений Azure](/azure/application-gateway/overview) — это подсистема балансировки нагрузки веб-трафика, предназначенная для управления трафиком веб-приложений. Традиционные подсистемы балансировки нагрузки перенаправляют трафик на конечный IP-адрес и порт, исходя из исходного IP-адреса и порта. Шлюз приложений обеспечивает более глубокий уровень управления, позволяя направлять трафик в зависимости от URL-адреса. Например, можно определить правило: если `images` — это путь URL-адреса, то трафик направляется на определенный набор серверов (известный как пул), настроенный для образов.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * настройка сети;
> * Создание двух экземпляров контейнеров Azure с помощью образа HTTPD
> * Создание шлюза приложений, работающего с экземплярами контейнеров Azure в пуле серверов

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В этом разделе код из сборника схем создает группу ресурсов Azure. Группа ресурсов — это логический контейнер, в котором настроены ресурсы Azure.  

Сохраните следующий сборник схем как `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

- Имя группы ресурсов — `myResourceGroup`. Это значение используется в данном руководстве.
- Группа ресурсов создается в расположении `eastus`.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов

Код из сборника схем в этом разделе создает виртуальную сеть, чтобы шлюз приложений мог обмениваться данными с другими ресурсами.

Сохраните следующий сборник схем как `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* Раздел `vars` содержит значения, которые используются для создания сетевых ресурсов. 
* Необходимо будет изменить эти значения для вашей среды.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Создание серверов

Код из сборника схем в этом разделе создает два экземпляра контейнеров Azure с помощью образов HTTPD, которые будут использоваться как веб-серверы для шлюза приложений.  

Сохраните следующий сборник схем как `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Код из сборника схем в этом разделе создает шлюз приложений `myAppGateway`.  

Сохраните следующий сборник схем как `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* `appGatewayIP` определяется в блоке `gateway_ip_configurations`. Для IP-конфигурации шлюза требуется ссылка на подсеть.
* `appGatewayBackendPool` определяется в блоке `backend_address_pools`. Для шлюза приложений необходимо наличие по крайней мере одного внутреннего пула адресов.
* `appGatewayBackendHttpSettings` определяется в блоке `backend_http_settings_collection`. Указывает, что для обмена данными используются порт 80 и протокол HTTP.
* `appGatewayHttpListener` определяется в блоке `backend_http_settings_collection`. Это прослушиватель по умолчанию, связанный с appGatewayBackendPool.
* `appGatewayFrontendIP` определяется в блоке `frontend_ip_configurations`. Он назначает адрес myAGPublicIPAddress для прослушивателя appGatewayHttpListener.
* `rule1` определяется в блоке `request_routing_rules`. Правило маршрутизации по умолчанию, связанное с прослушивателем appGatewayHttpListener.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook appgw_create.yml
```

Создание шлюза приложений может занять несколько минут.

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

1. В разделе [Создать группу ресурсов](#create-a-resource-group) необходимо указать расположение. Запишите это значение.

1. В разделе [Create network resources](#create-network-resources) (Создание сетевых ресурсов) нужно указать домен. Запишите это значение.

1. Для тестового URL-адреса замените расположение и домен, используя следующий формат: `http://<domain>.<location>.cloudapp.azure.com`.

1. Перейдите на тестовый URL-адрес.

1. Если отображается следующая страница, шлюз приложений работает должным образом.

    ![Успешное тестирование работающего шлюза приложений](media/application-gateway-configure/demo.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий код как `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Документация по Ansible в Azure](/azure/ansible/)