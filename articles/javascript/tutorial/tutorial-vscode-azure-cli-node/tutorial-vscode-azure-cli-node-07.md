---
title: Очистка ресурсов после развертывания приложения Node.js в Azure с помощью Azure CLI
description: Руководство, часть 7. Очистка ресурсов с помощью Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 247edae2c61b8c42f9ed99932ce8bdf567e0cb1e
ms.sourcegitcommit: 1dfcc022a3098b1a1505e9458eada35f527ef070
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97658449"
---
# <a name="part-7-clean-up-resources"></a>Часть 7. Очистка ресурсов

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-azure-cli-node-05.md)

Созданная Служба приложений включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы очистить ресурсы, выполните следующую команду в окне терминала или в командной строке:

```azurecli
az group delete --name myResourceGroup
```

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

> [!div class="nextstepaction"]
> [Готово](../../how-to/deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)