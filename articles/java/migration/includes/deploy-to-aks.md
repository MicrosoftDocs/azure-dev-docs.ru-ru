---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 29ea7925e91b1f42e0f83b88fbc7f7d9a8fd3629
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672880"
---
### <a name="deploy-to-aks"></a>Развертывание в AKS

Создайте и примените файлы YAML Kubernetes. Дополнительные сведения см. в [кратком руководстве Разверните кластер Службы Azure Kubernetes с помощью Azure CLI](/azure/aks/kubernetes-walkthrough#run-the-application). Если вы создаете внешний балансировщик нагрузки для приложения и контроллера входящего трафика, обязательно укажите IP-адрес (см. предыдущий раздел) в качестве `LoadBalancerIP`.

Включите внешние параметры в качестве переменных среды. См. сведения об [определении переменных среды для контейнера](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Не включайте секреты (например, пароли, ключи API и строки подключения JDBC). Они описаны в следующих разделах.

Не забудьте включить параметры памяти и ЦП при создании YAML развертывания, чтобы контейнеры имели правильный размер.
