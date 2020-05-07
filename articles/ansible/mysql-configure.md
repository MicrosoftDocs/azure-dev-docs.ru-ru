---
title: Руководство по настройке баз данных в Базе данных Azure для MySQL с помощью Ansible
description: Узнайте, как с помощью Ansible создать и настроить Базу данных Azure для сервера MySQL
keywords: ansible, azure, devops, bash, playbook, mysql, database
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e8aad724edd55bda7284eda5f9630e5ffbe9f74c
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743410"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Руководство по настройке баз данных в Базе данных Azure для MySQL с помощью Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[База данных Azure для MySQL](/azure/mysql/overview) — это служба реляционных баз данных на основе MySQL Community Edition. База данных Azure для MySQL позволяет управлять базами данных MySQL в веб-приложениях.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание сервера MySQL
> * Создание базы данных MySQL
> * Настройка правила брандмауэра для разрешения подключения к серверу внешних приложений
> * Подключение к серверу MySQL с помощью Azure Cloud Shell
> * Запрос доступных серверов MySQL
> * Получение списка всех баз данных на подключенных серверах

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В этом разделе код из сборника схем создает группу ресурсов Azure. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.  

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

* Создается группа ресурсов `myResourceGroup`.
* Группа ресурсов создается в расположении `eastus`.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Создание сервера и базы данных MySQL

Код из сборника схем в этом разделе создает сервер MySQL и экземпляр Базы данных Azure для MySQL. Новый сервер MySQL — это сервер 5-го поколения основного назначения с одним виртуальным ядром. Он называется `mysqlserveransible`. Экземпляр базы данных называется `mysqldbansible`.

Дополнительные сведения о ценовых категориях см. в статье [Ценовые категории службы "База данных Azure для MySQL"](/azure/mysql/concepts-pricing-tiers). 

Сохраните следующий сборник схем как `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* В разделе `vars` значение `mysqlserver_name` должно быть уникальным.
* В разделе `vars` замените `<server_admin_password>` паролем.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Настройка правила брандмауэра

Правило брандмауэра уровня сервера позволяет внешнему приложению подключиться к серверу через брандмауэр службы Azure MySQL. К примерам внешних приложений можно отнести программу командной строки `mysql` и MySQL Workbench.

Код из сборника схем в этом разделе создает правило брандмауэра `extenalaccess`, которое разрешает подключения с любого внешнего IP-адреса. 

Сохраните следующий сборник схем как `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Перед выполнением сборника схем ознакомьтесь со следующими указаниями.

* В разделе vars замените `startIpAddress` и `endIpAddress`. Используйте диапазон IP-адресов, которые соответствуют диапазону адресов, с которых будут устанавливаться подключения.
* Подключитесь к базе данных Azure для MySQL через порт 3306. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 3306 может быть запрещен. В таком случае вы не сможете подключиться к серверу. Для этого ваш ИТ-отдел должен открыть порт 3306.
* Сборник схем использует модуль `azure_rm_resource`, который позволяет напрямую использовать REST API.

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Подключение к серверу

В этом разделе вы примените Azure Cloud Shell для подключения к серверу, который вы создали ранее.

1. Откройте адрес shell.azure.com, нажав кнопку ниже.

   [![Внедрение запуска](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)

1. Введите приведенный ниже код.

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. В командной строке введите приведенную ниже команду, чтобы запросить состояние сервера.

    ```sql
    mysql> status
    ```
    
    Если она успешно выполнится, отобразятся результаты, аналогичные приведенным ниже.
    
    ```output
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Отправка запросов к серверам MySQL

Код из сборника схем в этом разделе отправляет запросы к серверам MySQL в `myResourceGroup` и выводит список баз данных на найденных серверах.

Сохраните следующий сборник схем как `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook mysql_query.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Вы увидите также следующие выходные данные для базы данных MySQL.

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий сборник схем как `cleanup.yml`:

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