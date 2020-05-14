---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 73f3893f22764280ed5d7c030e7c8eab7d2bf6b1
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988799"
---
Если приложение содержит код, зависящий от ОС узла, вам нужно выполнить рефакторинг кода для удаления этих зависимостей. Например, вам нужно заменить все символы `/` или `\`, используемые в путях файловой системы, на [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) или [`Paths.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).
