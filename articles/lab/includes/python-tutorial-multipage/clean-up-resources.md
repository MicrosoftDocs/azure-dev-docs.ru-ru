---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: cc541400419bf3269a7bb31b36fab788271539f4
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405232"
---
На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Группа ресурсов имеет имя, такое как "appsvc_rg_Linux_CentralUS", в зависимости от расположения. Если вы используете номер SKU Службы приложений, отличающийся от бесплатного уровня F1, за эти ресурсы будет взиматься плата (см. сведения о [ценах на Службу приложений](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Если эти ресурсы вам не понадобятся в будущем, удалите группу ресурсов, выполнив следующие команды:

```azurecli
az group delete --no-wait
```

Эта команда использует имя группы ресурсов, кэшированное в файле *.azure/config*.

Аргумент `--no-wait` позволяет команде возвращать сведения до завершения операции.