---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 6fdf908458eec428f4e01d1b5f20fcbf4e039dbe
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990226"
---
### <a name="configure-persistent-storage"></a>Настройка постоянного хранилища

Если какая либо часть приложения выполняет чтение или запись в локальную файловую систему, ее необходимо заменить, настроив постоянное хранилище. Дополнительные сведения см. в статье [Использование постоянного хранилища в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-persistent-storage).

Все временные файлы должны быть в каталоге `/tmp`. Узнать его расположение в любой ОС можно с помощью `System.getProperty("java.io.tmpdir")`. Для создания временных файлов можно также использовать [`java.nio.Files::createTempFile`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#createTempFile(java.lang.String,java.lang.String,java.nio.file.attribute.FileAttribute...)).
