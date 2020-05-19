---
title: Перенос приложений Spring Cloud в Azure Spring Cloud
description: В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Cloud для выполнения в Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 2/12/2020
ms.openlocfilehash: 31243bb89024b9c6106538425e0254e309eb4ba2
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990156"
---
# <a name="migrate-spring-cloud-applications-to-azure-spring-cloud"></a>Перенос приложений Spring Cloud в Azure Spring Cloud

В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Cloud для выполнения в Azure Spring Cloud.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь со следующими дополнительными руководствами по переносу:

* Перенос приложений на базе исполняемых JAR-файлов в контейнеры в Службе Azure Kubernetes (руководство ожидается)
* Перенос приложений на базе исполняемых JAR-файлов в Виртуальные машины Azure (руководство ожидается)

### <a name="inspect-application-components"></a>Проверка компонентов приложения

#### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Найдите все экземпляры, из которых выполняется чтение и (или) запись в локальной файловой системе. Найдите все фрагменты, из которых записываются и считываются временные файлы, файлы с коротким и длительным сроком хранения.

> [!NOTE]
> Azure Spring Cloud предоставляет на каждый экземпляр Azure Spring Cloud по 5 ГБ временного хранилища, подключенного к расположению `/tmp`. Если объемы записи временных файлов превысят этот лимит или файлы сохраняются в другом расположении, придется изменить соответствующий код.

<!-- The following two "static content" sections should be identical to the contents of includes\static-content.md except that here we use H5 headings. -->

##### <a name="read-only-static-content"></a>Статическое содержимое только для чтения

Если ваше приложение сейчас обслуживает статическое содержимое, вам потребуется альтернативное расположение для этого статического содержимого. Вы можете переместить статическое содержимое в хранилище BLOB-объектов Azure и включить Azure CDN для быстрого скачивания в глобальном масштабе. См. руководство по [размещению статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website) и [ по интеграции учетной записи хранения Azure с Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

##### <a name="dynamically-published-static-content"></a>Динамически опубликованное статическое содержимое

Если приложение допускает использование статического содержимого, которое передается или создается приложением и после этого становится неизменяемым, вы можете использовать хранилище BLOB-объектов Azure и Azure CDN, как описано выше, с Функциями Azure для выполнения отправки и обновления CDN. Практический пример реализации см. в руководстве по [отправке и предварительной загрузке статического содержимого CDN с помощью Функций Azure](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Определение того, содержат ли службы код, зависящий от ОС

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Для всех приложений, которые используют Spring Boot 1.x, выполните инструкции из [руководства по переходу на Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide), чтобы обновить их до поддерживаемой версии Spring Boot. Перечень поддерживаемых версий см. в руководстве [Подготовка приложения Java Spring к развертыванию](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

#### <a name="identify-spring-cloud-versions"></a>Определение версий Spring Cloud

Изучите все зависимости каждого из переносимых приложений, чтобы определить используемые в них версии компонентов Spring Cloud.

##### <a name="maven"></a>Maven

В проектах Maven версия Spring Cloud обычно указывается в свойстве `spring-cloud.version`:

```xml
  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
  </properties>
```

##### <a name="gradle"></a>Gradle

В проектах Gradle версия Spring Cloud обычно задается в блоке дополнительных свойств:

```gradle
ext {
  set('springCloudVersion', "Hoxton.SR3")
}
```

Вам следует обновить все приложения, чтобы они использовали поддерживаемые версии Spring Cloud. Список поддерживаемых версий см. в руководстве [Подготовка приложения Java Spring к развертыванию](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

#### <a name="identify-zipkin-dependencies"></a>Выявление зависимостей Zipkin

Определите, имеет ли приложение явные зависимости от Zipkin. Проверьте также зависимости от группы `io.zipkin.java` в известных зависимостях Maven или Gradle.

### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Определите внешние ресурсы, в том числе источники данных, брокеры сообщений JMS и URL-адреса других служб. В приложениях Spring Cloud конфигурацию для таких ресурсов обычно можно найти в одном из следующих расположений:

* В каталоге *src/main/directory*, обычно в файле с именем *application.properties* или *application.yml*.
* В репозитории конфигурации Spring Cloud, который мы определили на предыдущем шаге.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Когда вы найдете один или несколько используемых брокеров, получите их параметры. В приложениях Spring Cloud они обычно размещаются в файле *application.properties* или *application.yml* в каталоге приложения либо в репозитории сервера конфигурации Spring Cloud.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

#### <a name="identity-providers"></a>Поставщики удостоверений

Идентифицируйте всех поставщиков удостоверений и все приложения Spring Cloud, которые требуют проверки подлинности и (или) авторизации. Сведения о настройке поставщиков удостоверений вы найдете в следующих статьях.

* Конфигурация OAuth2 описана в статье [Краткое руководство по безопасности в Spring Cloud](https://cloud.spring.io/spring-cloud-static/spring-cloud-security/current/reference/html/#_quickstart).
* Сведения о настройке Auth0 в Spring Security см. в [документации по Auth0 для Spring Security](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Сведения о настройке PingFederate в Spring Security см. в [инструкциях по PingFederate для Auth0](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-pivotal-cloud-foundry-pcf"></a>Ресурсы, настраиваемые через Pivotal Cloud Foundry (PCF)

Для приложений, управляемых через Pivotal Cloud Foundry, внешние ресурсы (включая описанные выше) часто настраиваются с помощью привязок службы PCF. Чтобы проверить конфигурацию таких ресурсов, с помощью [Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/) узнайте значение переменной `VCAP_SERVICES` для приложения.

```bash
# Log into PCF, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <organization name>
cf target space <space name>

# Display variables for the application
cf env <Application Name>
```

В `VCAP_SERVICES` размещаются параметры конфигурации внешних служб, привязанных к приложению. Дополнительные сведения см. в документации по [PCF](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать в этом руководстве все возможные внешние зависимости. После миграции вам необходимо убедиться в том, что соблюдаются все внешние зависимости приложения.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-cloud](includes/inventory-configuration-sources-and-secrets-spring-cloud.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-cloud](includes/inspect-the-deployment-architecture-spring-cloud.md)]

## <a name="migration"></a>Миграция

### <a name="remove-explicit-configuration-server-settings"></a>Удаление явных параметров сервера конфигурации

В переносимых службах найдите все явные указания параметров Eureka и удалите их. Обычно эти параметры размещаются в файлах *application.properties* или *application.yml*:

**application.yml**

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://myusername:mysecretpassword@localhost:8761/eureka/
```

Если в конфигурации приложения есть параметр, похожий на этот пример, удалите его. Azure Spring Cloud будет автоматически добавлять сведения о подключении к серверу конфигурации.

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Создание экземпляра и приложений Azure Spring Cloud

Подготовьте экземпляр Azure Spring Cloud в подписке Azure. Теперь подготовьте приложение для каждой переносимой службы. Не включайте в них реестр и серверы конфигурации Spring Cloud. Вместо этого добавьте службу шлюза Spring Cloud. Инструкции см. в статье [Краткое руководство. Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="prepare-the-spring-cloud-config-server"></a>Подготовка сервера конфигурации Spring Cloud

Настройте сервер конфигурации в своем экземпляре Azure Spring Cloud. Дополнительные сведения см. в статье [Учебник. Настройка экземпляра сервера конфигурации Spring Cloud для службы](/azure/spring-cloud/spring-cloud-tutorial-config-server).

> [!NOTE]
> Если текущий репозиторий конфигурации Spring Cloud находится в локальной файловой системе или в локальной службе, необходимо первым делом перенести или реплицировать файлы конфигурации в репозиторий на основе частного облака, например GitHub, Azure Repos или BitBucket.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-spring-cloud-vault-secrets-to-azure-keyvault"></a>Миграция секретов из хранилища Spring Cloud в Azure Key Vault

Вы можете внедрять секреты прямо в приложения через Spring, используя начальный набор Spring Boot для Azure Key Vault. Дополнительные сведения см. в статье [Как использовать начальное приложение Spring Boot Starter с Azure Key Vault](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-key-vault).

> [!NOTE]
> Возможно, для миграции придется переименовать некоторые секреты. Вместе с этим обновите код приложения.

### <a name="migrate-all-certificates-to-keyvault"></a>Перенос всех сертификатов в Key Vault

Azure Spring Cloud не предоставляет доступ к хранилищу ключей JRE, поэтому вам придется перенести сертификаты в Azure Key Vault и изменить код приложения, чтобы он обращался к этим сертификатам в Key Vault. Дополнительные сведения см. в статьях [Начало работы с сертификатами Key Vault](/azure/key-vault/certificates/certificate-scenarios) и [Клиентская библиотека сертификатов Azure Key Vault для Java](/java/api/overview/azure/security-keyvault-certificates-readme).

### <a name="remove-application-performance-management-apm-integrations"></a>Удаление интеграции с системами управления производительностью приложений (APM)

Отмените любую интеграцию со средствами и (или) агентами APM. Сведения о настройке средств управления производительностью с помощью Azure Monitor см. в разделе, посвященном [действиям после миграции](#post-migration).

### <a name="replace-explicit-zipkin-dependencies-with-spring-cloud-starters"></a>Замена явных зависимостей Zipkin начальными наборами Spring Cloud

Если какое-либо из переносимых приложений имеет явные зависимости Zipkin, удалите их и замените начальными наборами Spring Cloud, как описано в разделе [Зависимость распределенной трассировки](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#distributed-tracing-dependency) руководства [Подготовка приложения Java Spring к развертыванию в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Сведения о распределенной трассировке с использованием Azure App Insights приводятся в разделе [о действиях после миграции](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Отключение клиентов и конечных точек метрик в приложениях

Удалите в приложениях все используемые клиенты метрик и предоставляемые конечные точки метрик.

### <a name="deploy-the-services"></a>Развертывание служб

Разверните все перенесенные микрослужбы (кроме серверов конфигурации и реестра Spring Cloud), как описано в кратком руководстве [ Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="configure-per-service-secrets-and-externalized-settings"></a>Настройка секретов и внешних параметров для каждой службы

Вы можете добавить любые параметры конфигурации для каждой службы в виде переменных среды. Выполните следующие действия на портале Azure:

1. Перейдите к экземпляру Azure Spring Cloud и выберите **Приложения**.
1. Выберите службу для настройки.
1. Щелкните **Конфигурация**.
1. Введите переменные для настройки.
1. Щелкните **Сохранить**.

![Параметры конфигурации для приложений Spring Cloud](media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>Миграция и включение поставщика удостоверений

Если какому-то из приложений Spring Cloud требуется проверка подлинности или авторизация, не забудьте настроить для них доступ к поставщику удостоверений:

* Если роль поставщика удостоверений выполняет Azure Active Directory, дополнительные изменения не требуются.
* Если роль поставщика удостоверений выполняет локальный лес Active Directory, примените решение гибридной идентификации на основе Azure Active Directory. Инструкции для этого есть в [документации по гибридной идентификации](/azure/active-directory/hybrid/).
* Если роль поставщика удостоверений выполняет другое локальное решение, например PingFederate, обратитесь к статье [Выборочная установка Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), чтобы настроить федерацию с Azure Active Directory. Кроме того, вы можете применить Spring Security для работы с поставщиком удостоверений через [OAuth2 и OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) или [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="update-client-applications"></a>Обновление клиентских приложений

Обновите конфигурацию всех клиентских приложений так, чтобы они использовали опубликованные конечные точки Azure Spring Cloud для перенесенных приложений.

## <a name="post-migration"></a>Действия после миграции

* Обдумайте возможность добавить конвейер развертывания для автоматизации и обеспечения согласованности этого процесса. Для этого существуют инструкции по настройке [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) и [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Обдумайте возможность применить промежуточные развертывания для тестирования изменений кода в рабочей среде, прежде чем предоставлять их некоторым или всем пользователям. Дополнительные сведения см. в статье [Настройка промежуточной среды в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Обдумайте возможность добавить привязки служб для подключения приложения к поддерживаемым базам данных Azure. Такие привязки служб избавляют от необходимости предоставлять сведения о подключении, включая учетные данные, для приложений Spring Cloud.

* Обдумайте возможность [применить распределенную трассировку и Azure App Insights](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing) для мониторинга производительности и взаимодействий приложений.

* Рассмотрите возможность добавить правила генерации оповещений и группы действий Azure Monitor для быстрого обнаружения и устранения отклонений от обязательных условий. Дополнительные сведения см. в статье [Учебник. Мониторинг ресурсов Spring Cloud с помощью оповещений и групп действий](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Обдумайте возможность реплицировать развертывание Azure Spring Cloud в другом регионе, чтобы снизить задержку, повысить надежность и отказоустойчивость. Примените [Диспетчер трафика Azure](/azure/traffic-manager) для балансировки нагрузки между развертываниями или [Azure Front Door](/azure/frontdoor) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.

* Если георепликация не требуется, попробуйте добавить [Шлюз приложений Azure](/azure/application-gateway) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.

* Если приложения используют устаревшие компоненты Spring Cloud Netflix, попробуйте заменить их более современными альтернативами.

   | Прежняя версия                        | Текущий                                                |
   |-------------------------------|--------------------------------------------------------|
   | Spring Cloud Eureka           | Реестр службы Spring Cloud                          |
   | Spring Cloud Netflix Zuul     | Spring Cloud Gateway                                   |
   | Spring Cloud Netflix Archaius | Сервер конфигурации Spring Cloud                             |
   | Spring Cloud Netflix Ribbon   | Spring Cloud Load Balancer (подсистема балансировки нагрузки на стороне клиента) |
   | Spring Cloud Hystrix          | Spring Cloud Circuit Breaker и Resilience4J            |
   | Spring Cloud Netflix Turbine  | Micrometer и Prometheus                                |
