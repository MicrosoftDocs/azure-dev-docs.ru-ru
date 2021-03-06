---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 885764615beed7a623db03499b4ff6a6ab705ba7
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831178"
---
#### <a name="ansible"></a>[Ansible](#tab/ansible)

1. Сохраните следующий код как `delete_rg.yml`.

    ```yml
    ---
    - hosts: localhost
      tasks:
        - name: Deleting resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            state: absent
          register: rg
        - debug:
            var: rg
    ```

1. Чтобы запустить сборник схем, выполните команду [ansible-playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html). Замените заполнитель именем удаляемой группы ресурсов. В указанной группе ресурсов будут удалены все ресурсы.

    ```bash
    ansible-playbook delete_rg.yml --extra-vars "name=<resource_group>"
    ```

    **Примечания**

    - В связи с наличием переменной `register` и раздела `debug` в сборнике схем результаты будут отображены после завершения команды.
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Чтобы удалить группу ресурсов, выполните команду [az group delete](/cli/azure/group#az_group_delete). В указанной группе ресурсов будут удалены все ресурсы.

    ```azurecli
    az group delete --name <resource_group>
    ```

1. Убедитесь, что группа ресурсов была удалена, с помощью команды [az group show](/cli/azure/group#az_group_show).

    ```azurecli
    az group show --name <resource_group>
    ```

#### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. Чтобы удалить группу ресурсов, выполните командлет [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup). В указанной группе ресурсов будут удалены все ресурсы.

    ```azurepowershell
    Remove-AzResourceGroup -Name <resource_group>
    ```

1. Убедитесь, что группа ресурсов была удалена, выполнив командлет [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup).

    ```azurepowershell
    Get-AzResourceGroup -Name <resource_group>
    ```

---