---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 2fc4e3b43a051103e7aea6aa77f66666ca454910
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672140"
---
<!-- Included in "### Switch to a supported platform" sections that have different (required) intro paragraphs. For example:

### Switch to a supported platform

App Service offers specific versions of Java SE. To ensure compatibility, migrate your application to one of the supported versions of in its current environment before you proceed with any of the remaining steps. Be sure to fully test the resulting configuration. Use the latest stable release of your Linux distribution in such tests.

-->

> [!NOTE]
> Эта проверка особенно важна, если на текущем сервере используется неподдерживаемая версия JDK (например, Oracle JDK или IBM OpenJ9).

Чтобы получить текущую версию Java, войдите на сервер в рабочей среде и выполните следующую команду:

```bash
java -version
```

Чтобы получить текущую версию, используемую в Службе приложений Azure, скачайте [Zulu 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk), если вы планируете использовать среду выполнения Java 8, или [Zulu 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts&architecture=x86-64-bit&package=jdk), если вы планируете использовать среду выполнения Java 11.
