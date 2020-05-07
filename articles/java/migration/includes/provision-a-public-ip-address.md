---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 5527a38151afd531cbd256428bd729b7aba8fe45
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673170"
---
### <a name="provision-a-public-ip-address"></a>Подготовка общедоступного IP-адреса

Если ваше приложение должно быть доступным за пределами внутренней или виртуальной сети, требуется общедоступный статический IP-адрес. Этот IP-адрес следует подготовить в группе ресурсов узла кластера, как показано в следующем примере:

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
