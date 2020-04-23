---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: cc634f89170f4db6f961da91e3eb3abe7393539d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673600"
---
### <a name="validate-that-the-supported-java-version-works-correctly"></a>Проверка правильной работы поддерживаемой версии Java

При использовании всех путей миграции в WebLogic в Azure требуется определенная соответствующая версия Java. Вам нужно проверить, может ли приложение правильно работать с этой поддерживаемой версией.

Чтобы получить текущую версию, войдите на рабочий сервер и выполните следующую команду:

```bash
java -version
```

> [!NOTE]
> При миграции на WebLogic на виртуальных машинах Azure требования к определенным версиям Java зависят от предварительно установленных версий Java на виртуальных машинах.
