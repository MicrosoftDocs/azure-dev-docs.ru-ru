---
title: Очистка ресурсов после развертывания приложения Node.js в Azure с помощью Azure CLI
description: Руководство, часть 7. Очистка ресурсов.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 6d0b9c671c4ffd0fb5d521e6247e9964d6d93980
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686104"
---
# <a name="clean-up-resources"></a>Очистка ресурсов

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-docker-node-06.md)

Созданная Служба приложений включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы очистить ресурсы, выполните следующую команду в окне терминала или в командной строке:

```bash
az group delete --name myResourceGroup
```

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
