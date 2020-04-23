---
title: Руководство по настройке управления доступом на основе ролей (RBAC) в Службе Azure Kubernetes с помощью Ansible
description: Узнайте, как с помощью Ansible настроить управление доступом на основе ролей (RBAC) в кластере Службы Azure Kubernetes (AKS).
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, aks, контейнер, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9018ca465246163a00e984d013449cfef6ae0be1
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743810"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Руководство по настройке ролей RBAC (управление доступом на основе ролей) в Службе Azure Kubernetes (AKS) с помощью Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

Службу Azure Kubernetes можно настроить для использования [Azure Active Directory (AD)](/azure/active-directory/) для аутентификации пользователей. После настройки можно будет использовать маркер аутентификации Azure AD для входа в кластер AKS. Управление доступом на основе ролей может основываться на удостоверении пользователя или членстве в группах каталога.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Создание кластера Azure AD с поддержкой AKS
> * Настройка роли RBAC в кластере

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Установите библиотеку RedHat OpenShift** - `pip install openshift`.

## <a name="configure-azure-ad-for-aks-authentication"></a>Настройка Azure AD для аутентификации AKS

При настройке Azure AD для аутентификации AKS настраиваются два приложения Azure AD. Эту операцию должен выполнять администратор клиента Azure. Дополнительные сведения см. в разделе [Интеграция Azure Active Directory со службой Azure Kubernetes](/azure/aks/aad-integration#create-the-server-application). 

Обратитесь к администратору клиента Azure, чтобы получить следующие значения:

- секрет серверного приложения;
- идентификатор серверного приложения;
- идентификатор клиентского приложения. 
- Tenant ID

Эти значения необходимы для запуска примера сборника схем.  

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

В этом разделе вы создаете службу AKS с помощью [приложения Azure AD](#configure-azure-ad-for-aks-authentication).

Ниже приведено несколько важных примечаний, которые следует рассмотреть при работе с примером сборника схем.

- Сборник схем выполняет загрузку `ssh_key` из `~/.ssh/id_rsa.pub`. При его изменении следует использовать однострочный формат, который начинается с "ssh-rsa" (без кавычек).
- Значения `client_id` и `client_secret` загружаются из `~/.azure/credentials`, который является файлом учетных данных по умолчанию. Эти значения можно установить в качестве субъекта-службы или загрузить их значения из переменных среды.

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Сохраните следующий сборник схем как `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Получение идентификатор объекта Azure AD

Чтобы создать привязку RBAC, сначала необходимо получить идентификатор объекта Azure AD. 

1. Войдите на [портал Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. В поле поиска в верхней части страницы введите `Azure Active Directory`. 

1. Нажмите кнопку `Enter`.

1. В меню **Управление** выберите **Пользователи**.

1. В поле имени найдите свою учетную запись.

1. В столбце **Имя** щелкните ссылку на свою учетную запись.

1. В разделе **Удостоверение** скопируйте **идентификатор объекта**.

    ![Скопируйте идентификатор объекта Azure AD.](./media/aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Создание привязки RBAC

В этом разделе вы создаете привязку роли или привязку роли кластера в AKS. 

Сохраните следующий сборник схем как `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Замените заполнитель `<your-aad-account>`[идентификатором объекта](#get-the-azure-ad-object-id) клиента Azure AD.

Сохраните приведенный ниже сборник схем, который развертывает новые роли в AKS, как `aks-kube-deploy.yml`.

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Выполнение примера сборника схем

В этом разделе перечислены полные примеры сборника схем, который в этой статье вызывает создание задач. 

Сохраните следующий сборник схем как `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

В разделе `vars` замените следующие заполнители своими данными Azure AD.

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Запустите готовый сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Проверка результатов

В этом разделе вы используете kubectl для получения списка узлов, созданных в этой статье.

В командной строке терминала введите следующую команду.

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Эта команда перенаправит вас на страницу аутентификации. Войдите в систему с использованием учетной записи Azure.

После аутентификации kubectl выведет список узлов, как показано ниже.

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этой статьи, если они вам больше не нужны. 

Сохраните следующий код как `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Запустите сборник схем с помощью команды `ansible-playbook`.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Документация по Ansible в Azure](/azure/ansible/)
