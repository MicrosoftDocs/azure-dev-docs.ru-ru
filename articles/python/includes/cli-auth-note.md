---
ms.openlocfilehash: f644d66895b7b8bcce345cd42c6efedfaddb4240
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764484"
---
В этом коде используется проверка подлинности на основе CLI (с помощью `AzureCliCredential`), так как в нем демонстрируются действия, которые можно выполнить непосредственно в Azure CLI. В обоих случаях для проверки подлинности используется одно и то же удостоверение.

Чтобы применить такой код в рабочем скрипте (например, для автоматизации управления виртуальной машиной), используйте `DefaultAzureCredential` (рекомендуется) или метод на основе субъекта-службы, как описано в статье [Проверка подлинности приложений Python с помощью служб Azure](../azure-sdk-authenticate.md).