---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 29ea7925e91b1f42e0f83b88fbc7f7d9a8fd3629
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897646"
---
### <a name="deploy-to-aks"></a>Развертывание в AKS

Создайте и примените файлы YAML Kubernetes. Дополнительные сведения см. в [кратком руководстве Разверните кластер Службы Azure Kubernetes с помощью Azure CLI](/azure/aks/kubernetes-walkthrough#run-the-application). Если вы создаете внешний балансировщик нагрузки для приложения и контроллера входящего трафика, обязательно укажите IP-адрес (см. предыдущий раздел) в качестве `LoadBalancerIP`.

Включите внешние параметры в качестве переменных среды. См. сведения об [определении переменных среды для контейнера](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Не включайте секреты (например, пароли, ключи API и строки подключения JDBC). Они описаны в следующих разделах.

Не забудьте включить параметры памяти и ЦП при создании YAML развертывания, чтобы контейнеры имели правильный размер.
