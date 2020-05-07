---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 39482a97063a244df785a7d3fecefe296f89a274
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673000"
---
### <a name="determine-whether-a-connection-to-on-premises-is-needed"></a>Определение того, нужно ли подключаться к локальной среде

Если вашему приложению требуется доступ к какой-либо из локальных служб, вам необходимо подготовить одну из служб подключения Azure. Дополнительные сведения см. в статье [Выбор решения для подключения локальной сети к Azure](/azure/architecture/reference-architectures/hybrid-networking/). Кроме того, необходимо выполнить рефакторинг приложения, чтобы использовать общедоступные API-интерфейсы, предоставляемые локальными ресурсами.
