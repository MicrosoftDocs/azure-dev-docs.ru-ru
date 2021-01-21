---
title: Удаление ресурса Azure
description: Очистите оплачиваемые ресурсы, удалив группу ресурсов с помощью команды Azure CLI.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: bc68f550d0a2c1bc1550eb755e6ef33bae6ae9b7
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561640"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. Очистка ресурсов для статического веб-приложения

Завершив работу с этим руководством, удалите группу ресурсов с ресурсом Компьютерного зрения и статическим веб-приложением, чтобы с вас не взималась плата за использование. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Чтобы удалить группу ресурсов, в том же окне терминала выполните команду [Azure CLI](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name rg-demo  -y
```

Эта команда может выполняться несколько минут. 
