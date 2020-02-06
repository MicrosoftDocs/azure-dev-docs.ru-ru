---
ms.openlocfilehash: 8f757c030849cb89eea36d74b55867dcc2ff98a6
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013832"
---
Группу ресурсов можно удалить с помощью [портала Azure](https://portal.azure.com) или Azure CLI.

- На портале щелкните **Группы ресурсов** в области навигации слева, выберите созданную с помощью этого руководства группу ресурсов и щелкните **Удалить группу ресурсов**.

- Выполните следующую команду Azure CLI (локально или с помощью [Cloud Shell](/azure/cloud-shell/overview)), заменив `<resource_group>` именем группы, используемой в этом руководстве.

    ```azurecli
    az group delete --name <resource_group>
    ```
