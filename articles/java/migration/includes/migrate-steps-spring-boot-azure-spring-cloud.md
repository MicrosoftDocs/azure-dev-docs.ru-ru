---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 7970cae2b9ac39f7012e74b1334d12b2c5006af0
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062068"
---
### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Создание экземпляра и приложений Azure Spring Cloud

Подготовьте экземпляр Azure Spring Cloud в подписке Azure, если он еще не существует. Затем создайте в нем приложение. Дополнительные сведения см. в [кратком руководстве Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](configure-persistent-storage-azure-spring-cloud.md)]

[!INCLUDE [migrate-all-certificates-to-keyvault-azure-spring-cloud](migrate-all-certificates-to-keyvault-azure-spring-cloud.md)]

### <a name="remove-application-performance-management-apm-integrations"></a>Удаление интеграции с системами управления производительностью приложений (APM)

Отмените любую интеграцию со средствами и (или) агентами APM. Сведения о настройке средств управления производительностью с помощью Azure Monitor см. в разделе, посвященном [действиям после миграции](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Отключение клиентов и конечных точек метрик в приложениях

Удалите в приложениях все используемые клиенты метрик и предоставляемые конечные точки метрик.

### <a name="deploy-the-application"></a>Развертывание приложения

Разверните все перенесенные микрослужбы (кроме серверов конфигурации и реестра Spring Cloud), как описано в статье [ Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="configure-per-service-secrets-and-externalized-settings"></a>Настройка секретов и внешних параметров для каждой службы

Вы можете добавить любые параметры конфигурации для каждой службы в виде переменных среды. Выполните следующие действия на портале Azure:

1. Перейдите к экземпляру Azure Spring Cloud и выберите **Приложения**.
1. Выберите службу для настройки.
1. Щелкните **Конфигурация**.
1. Введите переменные для настройки.
1. Щелкните **Сохранить**.

![Параметры конфигурации для приложений Spring Cloud](../media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>Миграция и включение поставщика удостоверений

Если какому-то из приложений Spring Cloud требуется проверка подлинности или авторизация, не забудьте настроить для них доступ к поставщику удостоверений:

* Если роль поставщика удостоверений выполняет Azure Active Directory, дополнительные изменения не требуются.
* Если роль поставщика удостоверений выполняет локальный лес Active Directory, примените решение гибридной идентификации на основе Azure Active Directory. Дополнительные сведения см. в [документации по гибридной идентификации](/azure/active-directory/hybrid/).
* Если роль поставщика удостоверений выполняет другое локальное решение, например PingFederate, обратитесь к статье [Выборочная установка Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), чтобы настроить федерацию с Azure Active Directory. Кроме того, вы можете применить Spring Security для работы с поставщиком удостоверений через [OAuth2 и OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) или [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="expose-the-application"></a>Предоставление доступа к приложению

По умолчанию приложения, развернутые в Azure Spring Cloud, не видны. Вы можете сделать свое приложение доступным, т. е. открытым, с помощью следующей команды:

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Пропустите этот шаг, если вы уже используете или планируете применить позднее шлюз Spring Cloud (дополнительные сведения об этом см. в следующем разделе).
