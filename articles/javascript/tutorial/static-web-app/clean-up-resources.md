---
title: Удаление ресурса Azure
description: Очистите оплачиваемые ресурсы, удалив группу ресурсов с помощью команды Azure CLI.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b425ce873c53ca95628cfe8c3f68ea4b010aba3
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993538"
---
# <a name="6-clean-up-resources"></a>6. Очистка ресурсов

Завершив работу с этим руководством, удалите группу ресурсов с ресурсом Компьютерного зрения и статическим веб-приложением, чтобы с вас не взималась плата за использование. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Чтобы удалить группу ресурсов, в том же окне терминала выполните команду [Azure CLI](/cli/azure/group?view=azure-cli-latest#az_group_delete):

```azurecli
az group delete --name rg-demo  -y
```

Эта команда может выполняться несколько минут. 
