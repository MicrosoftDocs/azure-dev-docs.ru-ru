---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: abf3824dd83d0da27a411694e144a8e6bf7285ac
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738495"
---
### <a name="deploy-to-aks"></a>Развертывание в AKS

Создайте и примените файлы YAML Kubernetes. Дополнительные сведения см. в [кратком руководстве Разверните кластер Службы Azure Kubernetes с помощью Azure CLI](/azure/aks/kubernetes-walkthrough#run-the-application). Если вы создаете внешний балансировщик нагрузки для приложения и контроллера входящего трафика, обязательно укажите IP-адрес (см. предыдущий раздел) в качестве `LoadBalancerIP`.

Включите внешние параметры в качестве переменных среды. См. сведения об [определении переменных среды для контейнера](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Не включайте секреты (например, пароли, ключи API и строки подключения JDBC). Они описаны в следующих разделах.

Не забудьте включить параметры памяти и ЦП при создании YAML развертывания, чтобы контейнеры имели правильный размер.
