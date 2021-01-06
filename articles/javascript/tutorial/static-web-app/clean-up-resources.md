---
title: Удаление ресурса Azure
description: Очистите оплачиваемые ресурсы, удалив группу ресурсов с помощью команды Azure CLI.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 132ccc26a4ddf17eb38be1573f462492fe1f7f0c
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687500"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. Очистка ресурсов для статического веб-приложения

Завершив работу с этим руководством, удалите группу ресурсов с ресурсом Компьютерного зрения и статическим веб-приложением, чтобы с вас не взималась плата за использование. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Чтобы удалить группу ресурсов, в том же окне терминала выполните команду [Azure CLI](/cli/azure/group?view=azure-cli-latest#az_group_delete):

```azurecli
az group delete --name rg-demo  -y
```

Эта команда может выполняться несколько минут. 
