---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 17790549afaee074625b8754080f15114e9aabef
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405302"
---
1. Подготовьте учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
1. Установите <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 или более поздней версии</a>.
1. Установите <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> версии 2.0.80 или более поздней для выполнения команд в любой оболочке для подготовки и настройки ресурсов Azure.

Откройте окно терминала и проверьте, что используется Python 3.6 или более поздней версии:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Убедитесь, что у вас есть Azure CLI версии 2.0.80 или выше.

```azurecli
az --version
```

Теперь войдите в Azure с помощью CLI.

```azurecli
az login
```

Эта команда открывает окно браузера для ввода учетных данных. После выполнения команда отображает выходные данные JSON с информацией о подписках.

Войдя, вы сможете выполнять в Azure CLI команды Azure для работы с ресурсами в подписке.
