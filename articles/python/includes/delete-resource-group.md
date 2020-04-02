---
ms.openlocfilehash: 8f757c030849cb89eea36d74b55867dcc2ff98a6
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441359"
---
Группу ресурсов можно удалить с помощью [портала Azure](https://portal.azure.com) или Azure CLI.

- На портале щелкните **Группы ресурсов** в области навигации слева, выберите созданную с помощью этого руководства группу ресурсов и щелкните **Удалить группу ресурсов**.

- Выполните следующую команду Azure CLI (локально или с помощью [Cloud Shell](/azure/cloud-shell/overview)), заменив `<resource_group>` именем группы, используемой в этом руководстве.

    ```azurecli
    az group delete --name <resource_group>
    ```
