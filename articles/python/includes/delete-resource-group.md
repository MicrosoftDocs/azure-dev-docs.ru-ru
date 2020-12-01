---
ms.openlocfilehash: c45bda8b08ec963febbc6497136cda71086423a3
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035500"
---
Группу ресурсов можно удалить с помощью [портала Azure](https://portal.azure.com) или Azure CLI.

- На [портале Azure](https://portal.azure.com) щелкните элемент **Группы ресурсов** в области навигации слева, выберите созданную с помощью этого руководства группу ресурсов и щелкните команду **Удалить группу ресурсов**.

- Выполните следующую команду Azure CLI (локально или с помощью [Cloud Shell](/azure/cloud-shell/overview)), заменив `<resource_group>` именем группы, используемой в этом руководстве.

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
