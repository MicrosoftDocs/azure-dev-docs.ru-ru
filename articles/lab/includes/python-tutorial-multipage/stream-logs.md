---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 5868697895ad067065627cca99f604d531dcb173
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405310"
---
Вы можете получить доступ к журналам консоли, созданным в приложении, и контейнеру, в котором он выполняется. Журналы содержат все выходные данные, созданные с помощью инструкций `print`.

Чтобы выполнить потоковую передачу журналов, запустите команду [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail):

```azurecli
az webapp log tail
```

Кроме того, вы можете использовать параметр `--logs`, а потом — команду `az webapp up`, чтобы автоматически запустить потоковую передачу журналов для развертывания.

Обновите приложение в браузере, чтобы создать журналы консоли, которые содержат сообщения с HTTP-запросами к приложению. Если выходные данные не отображаются немедленно, повторите попытку через 30 секунд.

Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы предотвратить потоковую передачу журналов, нажмите **Ctrl**+**C** в окне терминала.
