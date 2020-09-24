---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c8cd0672bfedf640077e9ae3b9272b12d8ce0b61
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738165"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Подготовка Реестра контейнеров Azure и Службы Kubernetes Azure

Используйте следующие команды, чтобы создать Реестр контейнеров и кластер Azure Kubernetes, субъект-служба которого имеет роль читателя в реестре. Не забудьте [выбрать соответствующую модель сети](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) в соответствии с требованиями к сети кластера.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
