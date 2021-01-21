---
title: Удаление ресурса виртуальной машины Linux
description: Очистите ресурсы Azure, удалив группу ресурсов с помощью команды Azure CLI.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: ac61f1b73e873ee1c1c6cd343792a79a88e76123
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98560990"
---
# <a name="7-clean-up-resources"></a>7. Очистка ресурсов

Завершив работу с этим руководством, удалите группу ресурсов со всеми ресурсами, чтобы с вас не взималась плата за использование. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Чтобы удалить группу ресурсов, в том же окне терминала выполните команду [Azure CLI](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name rg-demo-vm-eastus -y
```

Выполнение команды займет несколько минут. 

## <a name="next-step"></a>Следующий шаг

* Узнайте больше о [виртуальных машинах Linux в Azure](/azure/virtual-machines).
