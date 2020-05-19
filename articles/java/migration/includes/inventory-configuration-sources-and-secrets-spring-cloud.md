---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: b15ebf1491dd494701dd5c18e0248e73bdd86f2c
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990206"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Источники и секреты конфигурации инвентаризации

#### <a name="inventory-passwords-and-secure-strings"></a>Пароли и защищенные строки инвентаризации

Проверьте наличие секретных строк и паролей во всех свойствах и файлах конфигурации, а также всех переменных среды в рабочих развертываниях. В приложении Spring Cloud такие строки обычно размещаются в файле *application.properties* или *application.yml* в отдельных службах или в репозитории конфигурации Spring Cloud.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]

#### <a name="determine-whether-spring-cloud-vault-is-used"></a>Определение используемого хранилища Spring Cloud

Если для хранения и вызова секретов вы используете хранилище Spring Cloud, укажите резервное хранилище (например, HashiCorp или CredHub). Затем укажите все секреты, которые используются в коде приложения.

#### <a name="locate-the-configuration-server-source"></a>Определение сервера с данными о конфигурации

Если приложение использует [сервер конфигурации Spring Cloud](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server), найдите место хранения конфигурации. Обычно этот параметр настраивается в файле *bootstrap.yml* или *bootstrap.properties*, реже в файле *application.yml* или *application.properties* Он указывается так, как показано в примере ниже.

```properties
spring.cloud.config.server.git.uri: file://${user.home}/spring-cloud-config-repo
```

Чаще всего в качестве резервного хранилища данных для конфигурации Spring Cloud используется Git, как показано выше, но вместо этого может использоваться и любая другая из поддерживаемых серверных технологий. Дополнительные сведения о других серверных компонентах, таких как [реляционная база данных (JDBC)](https://cloud.spring.io/spring-cloud-config/reference/html/#_jdbc_backend), [SVN](https://cloud.spring.io/spring-cloud-config/reference/html/#_version_control_backend_filesystem_use) или [локальная файловая система](https://cloud.spring.io/spring-cloud-config/reference/html/#_file_system_backend), см. в [документации по конфигурации Spring Cloud](https://cloud.spring.io/spring-cloud-config/reference/html/#_environment_repository).

> [!NOTE]
> Если данные сервера конфигурации хранятся в локальной системе, например в GitHub Enterprise, их необходимо сделать доступными для Azure Spring Cloud через репозиторий Git.
