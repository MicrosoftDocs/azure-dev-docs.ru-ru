---
author: yevster
ms.author: yebronsh
ms.topic: include
ms.date: 1/20/2020
ms.openlocfilehash: 4228d1db44ac16da1c05fe97d20a3491cc9c5f51
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288573"
---
### <a name="inventory-secrets"></a>Проверка секретов

#### <a name="passwords-and-secure-strings"></a>Пароли и защищенные строки

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретных строк и паролей. Обязательно проверьте файлы *server.xml* и *context.xml* в каталоге $CATALINA_BASE/conf. Кроме того, в приложении есть файлы конфигурации, содержащие пароли или учетные данные. К ним может относиться файл *META-INF/context.xml*, а для приложений Spring Boot — файлы *application.properties* и *application.yml*.

#### <a name="certificates"></a>Сертификаты

Определите все сертификаты, используемые для общедоступных конечных точек SSL. Вы можете просмотреть все сертификаты на рабочих серверах, выполнив следующую команду:

```bash
keytool -list -v -keystore <path to keystore>
```
