---
title: Удаление ресурса виртуальной машины Linux
description: Очистите ресурсы Azure, удалив группу ресурсов с помощью команды Azure CLI.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5c8c0bb8a1413da72cb2c32d9ce541bee1c36cac
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872825"
---
# <a name="7-clean-up-resources"></a>7. Очистка ресурсов

Завершив работу с этим руководством, удалите группу ресурсов со всеми ресурсами, чтобы с вас не взималась плата за использование. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Чтобы удалить группу ресурсов, в том же окне терминала выполните команду [Azure CLI](/cli/azure/group?view=azure-cli-latest#az_group_delete):

```azurecli
az group delete --name rg-demo-vm-eastus -y
```

Выполнение команды займет несколько минут. 

## <a name="next-step"></a>Следующий шаг

* Узнайте больше о [виртуальных машинах Linux в Azure](/azure/virtual-machines).
