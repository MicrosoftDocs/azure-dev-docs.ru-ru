---
title: Перенос приложений Spring Boot в Azure Spring Cloud
description: В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Boot для выполнения в Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 7bc4a5188181f3b4b6d98b5308a5027a42bbac5e
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810587"
---
# <a name="migrate-spring-boot-applications-to-azure-spring-cloud"></a>Перенос приложений Spring Boot в Azure Spring Cloud

В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Boot для выполнения в Azure Spring Cloud.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь со следующими дополнительными руководствами по переносу:

* Перенос приложений на базе исполняемых JAR-файлов в контейнеры в Службе Azure Kubernetes (руководство ожидается)
* Перенос приложений на базе исполняемых JAR-файлов в Виртуальные машины Azure (руководство ожидается)

### <a name="inspect-application-components"></a>Проверка компонентов приложения

[!INCLUDE [identify-local-state](includes/identify-local-state-azure-spring-cloud.md)]

[!INCLUDE [static-content-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Определение того, содержат ли службы код, зависящий от ОС

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Для всех приложений, которые используют Spring Boot 1.x, выполните инструкции из [руководства по переходу на Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide), чтобы обновить их до поддерживаемой версии Spring Boot. Перечень поддерживаемых версий см. в руководстве [Подготовка приложения Java Spring к развертыванию](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Определите внешние ресурсы, в том числе источники данных, брокеры сообщений JMS и URL-адреса других служб. В приложениях Spring Boot файл с конфигурацией этих ресурсов обычно размещается в каталоге *src/main/directory* и называется *application.properties* или *application.yml*.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Когда вы найдете один или несколько используемых брокеров, получите их параметры. В приложениях Spring Boot они обычно размещаются в файле *application.properties* или *application.yml* в каталоге приложения.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot.md](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="identify-any-clients-relying-on-a-non-standard-port"></a>Поиск клиентов, которые используют нестандартный порт

Azure Spring Cloud перезаписывает параметр `server.port` в развернутом приложении. Если клиенты ваших клиентов ожидают, что приложение будет доступно через порт, отличающийся от 443, их необходимо изменить.

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать в этом руководстве все возможные внешние зависимости. После миграции вам необходимо убедиться в том, что соблюдаются все внешние зависимости приложения.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

## <a name="migration"></a>Миграция

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Создание экземпляра и приложений Azure Spring Cloud

Подготовьте экземпляр Azure Spring Cloud в подписке Azure, если он еще не существует. Затем создайте в нем приложение. Дополнительные сведения см. в [кратком руководстве Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-all-certificates-to-keyvault"></a>Перенос всех сертификатов в Key Vault

Azure Spring Cloud не предоставляет доступ к хранилищу ключей JRE, поэтому вам придется перенести сертификаты в Azure Key Vault и изменить код приложения, чтобы он обращался к этим сертификатам в Key Vault. Дополнительные сведения см. в статьях [Начало работы с сертификатами Key Vault](/azure/key-vault/certificates/certificate-scenarios) и [Клиентская библиотека сертификатов Azure Key Vault для Java](/java/api/overview/azure/security-keyvault-certificates-readme).

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

![Параметры конфигурации для приложений Spring Cloud](media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

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

## <a name="post-migration"></a>Действия после миграции

Итак, вы завершили миграцию. Теперь убедитесь, что приложение работает правильно. Применив указанные ниже рекомендации, вы сможете сделать приложение более удобным для использования в облаке.

* Оцените возможность настроить работу приложения с реестром Spring Cloud. Это позволит другим развернутым микрослужбам и клиентам динамически обнаруживать это приложение. Дополнительные сведения см. в статье [Учебник. по подготовке приложения Java Spring для развертывания в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Затем измените все клиенты приложений, чтобы они использовали подсистему балансировки нагрузки клиентов Spring. Это позволит клиенту получать адреса всех работающих экземпляров приложения и находить работающий экземпляр, когда нарушается работа другого экземпляра или он не отвечает. Дополнительные сведения см. в записи [Spring Tips: Spring Cloud Loadbalancer](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) (Советы по Spring: подсистема балансировки нагрузки в Spring Cloud) блога, посвященного Spring Cloud.

* Вместо того, чтобы делать приложение общедоступным, попробуйте добавить экземпляр [шлюза Spring Cloud](https://cloud.spring.io/spring-cloud-gateway/reference/html/). Шлюз Spring Cloud выполняет роль единой конечной точки для всех приложений и микрослужб, развернутых в экземпляре Spring Cloud. Если шлюз Spring Cloud уже развернут, настройте маршрутизацию трафика в только что развернутое приложение.

* Попробуйте добавить сервер облачной конфигурации Spring Cloud для централизованного управления всеми микрослужбами Spring Cloud и настройки управления версиями. Сначала создайте репозиторий Git для размещения конфигурации и настройте его использование в экземпляре Azure Spring Cloud. Дополнительные сведения см. в статье [Учебник. Настройка экземпляра сервера конфигурации Spring Cloud для службы](/azure/spring-cloud/spring-cloud-tutorial-config-server). Затем перенесите конфигурацию, выполнив следующие действия:

  1. Создайте каталог в репозитории конфигурации Git с тем же именем, что и у приложения, определенного для экземпляра Azure Spring Cloud.

  1. В этом каталоге создайте файл *bootstrap.yml* со следующим содержимым:

     ```yml
     spring:
       application:
         name: <Your the application name used in the previous step>
     ```

  1. Создайте файл *application.yml* в указанном выше каталоге, а затем переместите туда параметры приложения. Если ранее параметры размещались в файле *.properties*, их следует преобразовать в формат YAML.

  1. Зафиксируйте эти изменения и отправьте их в репозиторий Git.

* Обдумайте возможность добавить конвейер развертывания для автоматизации и обеспечения согласованности этого процесса. Для этого существуют инструкции по настройке [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) и [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Обдумайте возможность применить промежуточные развертывания для тестирования изменений кода в рабочей среде, прежде чем предоставлять их некоторым или всем пользователям. Дополнительные сведения см. в статье [Настройка промежуточной среды в Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Обдумайте возможность добавить привязки служб для подключения приложения к поддерживаемым базам данных Azure. Такие привязки служб избавляют от необходимости предоставлять сведения о подключении, включая учетные данные, для приложений Spring Cloud.

* Попробуйте применить распределенную трассировку и Azure App Insights для мониторинга производительности и взаимодействий приложений. См. статью [Использование распределенной трассировки с помощью Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Рассмотрите возможность добавить правила генерации оповещений и группы действий Azure Monitor для быстрого обнаружения и устранения отклонений от обязательных условий. Дополнительные сведения см. в статье [Учебник. Мониторинг ресурсов Spring Cloud с помощью оповещений и групп действий](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Обдумайте возможность реплицировать развертывание Azure Spring Cloud в другом регионе, чтобы снизить задержку, повысить надежность и отказоустойчивость. Примените [Диспетчер трафика Azure](/azure/traffic-manager) для балансировки нагрузки между развертываниями или [Azure Front Door](/azure/frontdoor) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.

* Если георепликация не требуется, попробуйте добавить [Шлюз приложений Azure](/azure/application-gateway) для добавления разгрузки SSL и брандмауэра веб-приложения с защитой от атак DDoS.
