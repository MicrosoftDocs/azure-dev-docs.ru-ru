---
ms.openlocfilehash: 4215099ae39963448b7a94d389ded0c9096b1c67
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772685"
---
Группу ресурсов можно удалить с помощью [портала Azure](https://portal.azure.com) или Azure CLI.

- На портале щелкните **Группы ресурсов** в области навигации слева, выберите созданную с помощью этого руководства группу ресурсов и щелкните **Удалить группу ресурсов**.

- Выполните следующую команду Azure CLI (локально или с помощью [Cloud Shell](/azure/cloud-shell/overview)), заменив `<resource_group>` именем группы, используемой в этом руководстве.

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
