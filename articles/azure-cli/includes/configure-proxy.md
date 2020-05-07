---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 05/28/2019
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 676f33377a4e7122941bc789c51465b7f34aa1d3
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030886"
---
Если вы не можете подключиться к внешнему ресурсу из-за прокси-сервера, убедитесь, что вы правильно задали переменные `HTTP_PROXY` и `HTTPS_PROXY` в оболочке. Обратитесь к системному администратору, чтобы узнать, какие узлы и порты использовать для этих прокси-серверов.

Эти значения учитываются многими программами Linux, включая те, которые используются в процессе установки. Чтобы задать эти значения, сделайте следующее:

```bash
# No auth
export HTTP_PROXY=http://[proxy]:[port]
export HTTPS_PROXY=https://[proxy]:[port]

# Basic auth
export HTTP_PROXY=http://[username]:[password]@[proxy]:[port]
export HTTPS_PROXY=https://[username]:[password]@[proxy]:[port]
```

> [!IMPORTANT]
> Если вы работаете за прокси-сервером, эти переменные оболочки должны быть заданы для подключения к службам Azure с помощью CLI.
> Если вы не используете обычную аутентификацию, рекомендуется экспортировать эти переменные в файл `.bashrc`.
> Всегда следуйте корпоративным политикам безопасности и учитывайте требования системного администратора.
