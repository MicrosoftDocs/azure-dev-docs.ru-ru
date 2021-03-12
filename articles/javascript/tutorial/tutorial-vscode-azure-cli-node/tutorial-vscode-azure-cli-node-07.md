---
title: Очистка ресурсов после развертывания приложения Node.js в Azure с помощью Azure CLI
description: Руководство, часть 7. Очистка ресурсов с помощью Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d2b45ae60a7ad1270547289d1ea8480d14fedd95
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117968"
---
# <a name="part-7-clean-up-resources"></a>Часть 7. Очистка ресурсов

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-azure-cli-node-05.md)

Созданная Служба приложений включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы очистить ресурсы, выполните следующую команду в окне терминала или в командной строке:

```azurecli
az group delete --name myResourceGroup
```

> [!div class="nextstepaction"]
> [Готово](../../how-to/deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)