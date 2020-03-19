---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 5527a38151afd531cbd256428bd729b7aba8fe45
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897595"
---
### <a name="provision-a-public-ip-address"></a>Подготовка общедоступного IP-адреса

Если ваше приложение должно быть доступным за пределами внутренней или виртуальной сети, требуется общедоступный статический IP-адрес. Этот IP-адрес следует подготовить в группе ресурсов узла кластера, как показано в следующем примере:

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
