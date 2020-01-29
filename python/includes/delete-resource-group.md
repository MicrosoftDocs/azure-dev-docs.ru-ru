---
ms.openlocfilehash: 2f54e918e7126123ada68b696ea96f4d5f3dbb83
ms.sourcegitcommit: a8073315f751631ab983618fa9f812eb95d8b2dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125245"
---
Группу ресурсов можно удалить с помощью [портала Azure](https://portal.azure.com) или Azure CLI.

- На портале щелкните **Группы ресурсов** в области навигации слева, выберите созданную с помощью этого руководства группу ресурсов и щелкните **Удалить группу ресурсов**.

- Выполните следующую команду Azure CLI (локально или с помощью [Cloud Shell](/cloud-shell/overview)), заменив `<resource_group>` именем группы, используемой в этом руководстве.

    ```azurecli
    az group delete --name <resource_group>
    ```
