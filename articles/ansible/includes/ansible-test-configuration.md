---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 5351445b63618d072ecf4cf8beeffc9a071bd84d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682037"
---
В этом разделе демонстрируется, как создать тестовую группу ресурсов в новой конфигурации Ansible. Если это не требуется, вы можете пропустить этот раздел.

### <a name="create-an-azure-resource-group"></a>создание группы ресурсов Azure;

1. Сохраните следующий код как `create_rg.yml`.

    ```yaml
    ---
    - hosts: localhost
      connection: local
      tasks:
        - name: Creating resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            location: "{{ location }}"
          register: rg
        - debug:
            var: rg
    ```

1. Запустите сборник схем с помощью команды [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html). Замените заполнители именем и расположением создаваемой группы ресурсов.

    ```bash
    ansible-playbook create_rg.yml --extra-vars "name=<resource_group_name> location=<resource_group_location>"
    ```

    **Примечания**

    - В связи с наличием переменной `register` и раздела `debug` в сборнике схем результаты будут отображены после завершения команды.

### <a name="delete-an-azure-resource-group"></a>Удаление группы ресурсов Azure

[!INCLUDE [ansible-delete-resource-group.md](ansible-delete-resource-group.md)]
