---
title: Руководство по настройке приложений в Службе приложений Azure с помощью Ansible
description: Узнайте, как создать приложение со средой выполнения контейнеров Java 8 и Tomcat в Службе приложений Azure.
keywords: ansible, azure, devops, bash, playbook, azure app service, web app, java
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 13ef1291f2d61f5792d2d21fa105a1dcd9f1e2d3
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681749"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Руководство по настройке приложений в Службе приложений Azure с помощью Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание приложения со средой выполнения контейнеров Java 8 и Tomcat в Службе приложений Azure.
> * Создание профиля диспетчера трафика Azure.
> * Определение конечной точки диспетчера трафика с помощью созданного приложения.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Создание службы приложений (цен. категория "Базовый")

Код из сборника схем в этом разделе определяет следующие ресурсы:

* группа ресурсов Azure, в которой развертываются план службы приложений и приложение;
* служба приложений со средой выполнения контейнеров Java 8 и Tomcat;

Сохраните следующий сборник схем как `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Запустите сборник схем с помощью команды [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html).

```bash
ansible-playbook firstwebapp.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Создание приложения и использование диспетчера трафика Azure

С помощью [диспетчера трафика Azure](/azure/app-service/web-sites-traffic-manager) можно управлять распределением запросов от веб-клиентов между приложениями в Службе приложений Azure. Диспетчер трафика отслеживает состояние приложений службы при добавлении конечных точек службы приложений в профиль диспетчера трафика Azure. Состояния включают запуск, остановку и удаление. Диспетчер трафика принимает решение, какая из этих конечных точек должна получить трафик.

В службе приложений приложение выполняется в [плане службы приложений](/azure/app-service/overview-hosting-plans). План службы приложений определяет набор вычислительных ресурсов, на которых выполняется приложение. Вы можете управлять планом службы приложений и веб-приложением в разных группах.

Код из сборника схем в этом разделе определяет следующие ресурсы:

* группа ресурсов Azure, в которой развертываются план службы приложений и приложение;
* План службы приложений
* группа ресурсов Azure, в которой развертывается приложение;
* служба приложений со средой выполнения контейнеров Java 8 и Tomcat;
* профиль диспетчера трафика;
* конечная точка диспетчера трафика, использующая созданное приложение.

Сохраните следующий сборник схем как `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Запустите сборник схем с помощью команды [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html).

```bash
ansible-playbook webapp.yml
```

После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Руководство. масштабированию приложений в Службе приложений Azure с помощью Ansible](/azure/ansible/ansible-scale-azure-web-apps)