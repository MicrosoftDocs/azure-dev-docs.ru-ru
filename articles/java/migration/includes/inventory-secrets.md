---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: e37d0337361ce742ce7f7994ab634e63bc4b2ead
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81671650"
---
### <a name="inventory-secrets"></a>Проверка секретов

#### <a name="passwords-and-secure-strings"></a>Пароли и защищенные строки

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретных строк и паролей. Обязательно проверьте файлы *server.xml* и *context.xml* в каталоге *$CATALINA_BASE/conf*. Кроме того, в приложении есть файлы конфигурации, содержащие пароли или учетные данные. К ним может относиться файл *META-INF/context.xml*, а для приложений Spring Boot — файлы *application.properties* и *application.yml*.
