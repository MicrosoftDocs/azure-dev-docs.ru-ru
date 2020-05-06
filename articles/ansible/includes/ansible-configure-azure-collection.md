---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/09/2020
ms.author: tarcher
ms.openlocfilehash: 4f1fbe75f974aadc9e92e518e0e84d49ee73ed3d
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81755227"
---
- **Настройка коллекции Azure.** Выполните приведенную ниже команду в окне терминала, чтобы установить коллекцию Azure. Если коллекция Azure уже установлена, при использовании флага `--force` она будет обновлена до новейшей версии.

    ```bash
    ansible-galaxy collection install azure.azcollection --force
    ```
