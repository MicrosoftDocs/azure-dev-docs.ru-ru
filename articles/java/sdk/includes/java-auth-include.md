---
ms.openlocfilehash: 0a9b06932baa51c3cf003a4485a3a25261ffe91d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81671870"
---
Создайте [файл аутентификации](../java-sdk-azure-authenticate.md#mgmt-file), экспортируйте переменную среды `AZURE_AUTH_LOCATION` в командной строке и укажите полный путь к файлу.

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azure.auth
```

Файл аутентификации позволяет настроить объект `Azure` точки входа, используемый библиотеками управления для определения, создания и настройки ресурсов Azure.

```java
// pull in the location of the security file from the environment 
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```

См. [дополнительные сведения](../java-sdk-azure-authenticate.md#mgmt-auth) о способах аутентификации при использовании библиотек управления Azure для Java.