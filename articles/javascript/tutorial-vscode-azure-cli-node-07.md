---
title: Очистка ресурсов после развертывания приложения Node.js в Azure с помощью Azure CLI
description: Руководство, часть 7. Очистка ресурсов с помощью Azure CLI
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 0c3404067f6324a37d7863568e715b806511a226
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405993"
---
# <a name="part-7-clean-up-resources"></a>Часть 7. Очистка ресурсов

[Предыдущий шаг. Внесение изменений и повторное развертывание](tutorial-vscode-docker-node-06.md)

Созданная Служба приложений включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы очистить ресурсы, выполните следующую команду в окне терминала или в командной строке:

```azurecli
az group delete --name myResourceGroup
```

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

> [!div class="nextstepaction"]
> [Готово](./how-to/deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)