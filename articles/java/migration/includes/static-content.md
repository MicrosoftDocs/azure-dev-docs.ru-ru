---
author: yevster
ms.author: yebronsh
ms.date: 4/10/2020
ms.openlocfilehash: c6f4f3b58ff7d5a8733a0f2df2ab0bf3c6eb6fe4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988730"
---
#### <a name="read-only-static-content"></a>Статическое содержимое только для чтения

Если ваше приложение сейчас обслуживает статическое содержимое, вам потребуется альтернативное расположение для этого статического содержимого. Вы можете переместить статическое содержимое в хранилище BLOB-объектов Azure и включить Azure CDN для быстрого скачивания в глобальном масштабе. См. руководство по [размещению статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website) и [ по интеграции учетной записи хранения Azure с Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Динамически опубликованное статическое содержимое

Если приложение допускает использование статического содержимого, которое передается или создается приложением и после этого становится неизменяемым, вы можете использовать хранилище BLOB-объектов Azure и Azure CDN, как описано выше, с Функциями Azure для выполнения отправки и обновления CDN. Практический пример реализации см. в руководстве по [отправке и предварительной загрузке статического содержимого CDN с помощью Функций Azure](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
