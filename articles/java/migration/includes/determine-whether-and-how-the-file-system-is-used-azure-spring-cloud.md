---
author: yevster
ms.author: yebronsh
ms.date: 5/26/2020
ms.openlocfilehash: 30d39530700806dc910c085c36a7834efa9c1414
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507613"
---
#### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Найдите все экземпляры, из которых выполняется чтение и (или) запись в локальной файловой системе. Найдите все фрагменты, из которых записываются и считываются временные файлы, файлы с коротким и длительным сроком хранения.

> [!NOTE]
> Azure Spring Cloud предоставляет на каждый экземпляр Azure Spring Cloud по 5 ГБ временного хранилища, подключенного к расположению `/tmp`. Если объемы записи временных файлов превысят этот лимит или файлы сохраняются в другом расположении, придется изменить соответствующий код.

<!-- The following two "static content" sections should be identical to the contents of includes\static-content.md except that here we use H5 headings. -->

##### <a name="read-only-static-content"></a>Статическое содержимое только для чтения

Если ваше приложение сейчас обслуживает статическое содержимое, вам потребуется альтернативное расположение для этого статического содержимого. Вы можете переместить статическое содержимое в хранилище BLOB-объектов Azure и включить Azure CDN для быстрого скачивания в глобальном масштабе. См. руководство по [размещению статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website) и [ по интеграции учетной записи хранения Azure с Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

##### <a name="dynamically-published-static-content"></a>Динамически опубликованное статическое содержимое

Если приложение допускает использование статического содержимого, которое передается или создается приложением и после этого становится неизменяемым, вы можете использовать хранилище BLOB-объектов Azure и Azure CDN, как описано выше, с Функциями Azure для выполнения отправки и обновления CDN. Практический пример реализации см. в руководстве по [отправке и предварительной загрузке статического содержимого CDN с помощью Функций Azure](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
