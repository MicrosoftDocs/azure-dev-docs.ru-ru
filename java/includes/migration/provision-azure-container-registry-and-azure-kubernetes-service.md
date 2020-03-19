---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e72cefaab3ccdbbaae01992cc11285944fb09a36
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897425"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Подготовка Реестра контейнеров Azure и Службы Kubernetes Azure

Используйте следующие команды, чтобы создать Реестр контейнеров и кластер Azure Kubernetes, субъект-служба которого имеет роль читателя в реестре. Не забудьте [выбрать соответствующую модель сети](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) в соответствии с требованиями к сети кластера.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
