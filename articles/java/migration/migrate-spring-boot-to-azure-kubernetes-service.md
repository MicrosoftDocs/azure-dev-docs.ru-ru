---
title: Перенос приложений Spring Boot для выполнения в Службе Azure Kubernetes
description: Сведения о том, что важно учитывать при переносе существующего приложения Spring Boot для выполнения в контейнере Службы Azure Kubernetes.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 4/10/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 77ad38a4fb1290e392ee933a04aaf802a910e577
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689046"
---
# <a name="migrate-spring-boot-applications-to-azure-kubernetes-service"></a>Перенос приложений Spring Boot в Службу Azure Kubernetes

В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Boot для запуска в Службе Azure Kubernetes (AKS).

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Проверка правильной работы поддерживаемой версии Java

Мы рекомендуем использовать поддерживаемую версию Java для запуска приложений Spring Boot в AKS. Проверьте, может ли приложение правильно работать с этой поддерживаемой версией.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Для использования файловой системы в приложении Spring Boot потребуется изменение конфигурации или даже архитектуры. Вы можете определить некоторые или все сценарии, описанные в следующих разделах.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Для всех приложений, которые используют Spring Boot 1.x, выполните инструкции из [руководства по переходу на Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide), чтобы обновить их до поддерживаемой версии Spring Boot.

### <a name="review-your-database-properties"></a>Проверка свойств базы данных

Если приложение использует базу данных, проверьте ее свойства в файле *application.properties* , чтобы приложение Spring Boot сохранило доступ к базе данных после переноса в AKS. Если база данных расположена в локальной среде, придется либо перенести ее в облако, либо настроить подключение к локальной базе данных.

### <a name="identify-log-aggregation-solutions"></a>Определение решений по объединению журналов

Найдите все решения по объединению журналов, которые используются переносимыми приложениями.

### <a name="identify-application-performance-management-apm-agents"></a>Определение агентов управления производительностью приложений

Найдите все агенты наблюдения за производительностью приложений, которые используются в этих приложениях (например, Dynatrace или Datadog). Вам придется перенастроить эти агенты, чтобы включить их в конфигурацию Dockerfile или Jib, либо применить внутрипроцессный агент Java из Application Insights.

### <a name="identify-zipkin-dependencies"></a>Выявление зависимостей Zipkin

Определите, имеет ли приложение явные зависимости от Zipkin. Проверьте также зависимости от группы `io.zipkin.java` в известных зависимостях Maven или Gradle.

### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Определите внешние ресурсы, в том числе источники данных, брокеры сообщений JMS и URL-адреса других служб. В приложениях Spring Boot файл с конфигурацией этих ресурсов обычно размещается в каталоге *src/main/directory* и называется *application.properties* или *application.yml* . Кроме того, проверьте наличие параметров конфигурации в переменных среды в рабочем развертывании.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Когда вы найдете один или несколько используемых брокеров, получите их параметры. В приложениях Spring Boot они обычно размещаются в файле *application.properties* или *application.yml* в каталоге приложения.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

#### <a name="identity-providers"></a>Поставщики удостоверений

Идентифицируйте всех поставщиков удостоверений и все приложения Spring Boot, которые требуют проверки подлинности и (или) авторизации. Сведения о настройке поставщиков удостоверений вы найдете в следующих статьях.

* Для настройки OAuth или OAuth2 в Spring Security воспользуйтесь документацией по [Spring Security](https://spring.io/projects/spring-security).
* Сведения о настройке Auth0 в Spring Security см. в [документации по Auth0 для Spring Security](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Сведения о настройке PingFederate в Spring Security см. в [инструкциях по PingFederate для Auth0](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-vmware-tanzu-application-service-tas-formerly-pivotal-cloud-foundry"></a>Ресурсы, настроенные с помощью службы VMware Tanzu Application Service (TAS) (ранее — Pivotal Cloud Foundry)

Для приложений, управляемых через TAS, внешние ресурсы (включая описанные выше) часто настраиваются с помощью привязок службы TAS. Чтобы проверить конфигурацию таких ресурсов, с помощью [TAS (Cloud Foundry) CLI](https://docs.cloudfoundry.org/cf-cli/) узнайте значение переменной `VCAP_SERVICES` для приложения.

```bash
# Log into TAS, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <Organization Name>
cf target space <Space Name>

# Display variables for the application
cf env <Application Name>
```

В переменной `VCAP_SERVICES` размещаются параметры конфигурации внешних служб, привязанных к приложению. См. сведения в [документации по TAS (Cloud Foundry)](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

### <a name="in-place-testing"></a>Тестирование на месте

Прежде чем создавать образы контейнеров, перенесите приложение на те версии JDK и Spring Boot, которые вы намерены использовать в AKS. Тщательно протестируйте приложение, чтобы обеспечить совместимость и высокую производительность.

## <a name="migration"></a>Миграция

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

### <a name="create-a-docker-image-for-spring-boot"></a>Создание образа Docker для Spring Boot

Чтобы создать Dockerfile, вам потребуется следующее:

* поддерживаемый пакет JDK;
* среда выполнения JVM;
* способ передачи переменных среды (если применимо).

Затем вы можете выполнить действия, описанные в следующих разделах, насколько они применимы. В качестве отправной точки для работы с приложением Spring Boot можно использовать [репозиторий для быстрого начала работы с контейнерами Spring Boot](https://github.com/Azure/spring-boot-container-quickstart).

#### <a name="configure-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Настройка поставщика услуг Azure Key Vault для драйвера CSI хранилища секретов

Создайте хранилище ключей в Azure и укажите все необходимые секреты. Дополнительные сведения см. в [кратком руководстве Настройка и получение секрета из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli). Затем настройте [поставщик Azure Key Vault для драйвера CSI хранилища секретов](https://github.com/Azure/secrets-store-csi-driver-provider-azure), чтобы сделать эти секреты доступными для объектов pod.

Кроме того, потребуется обновить скрипт запуска, который выполняет начальную загрузку приложения Spring Boot. Этот скрипт должен перед запуском приложения импортировать сертификаты в хранилище ключей, используемое для Spring Boot.

### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Сборка и передача образа Docker в Реестр контейнеров Azure

После создания Dockerfile необходимо создать образ Docker и опубликовать его в Реестре контейнеров Azure.

Если вы использовали наш [GitHub-репозиторий для быстрого начала работы с контейнерами Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), процесс создания образа и его передачи в реестр контейнеров Azure будет выполняться следующими тремя командами.

В этих примерах переменная среды `MY_ACR` содержит имя Реестра контейнеров Azure, а переменная `MY_APP_NAME` содержит имя веб-приложения, используемого в Реестре контейнеров Azure.

Сборка файла развертывания:

```bash
mvn package
```

Войдите в Реестр контейнеров Azure:

```azurecli
az acr login -n ${MY_ACR}
```

Сборка и передача образа:

```azurecli
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} .
```

Кроме того, с помощью Docker CLI можно сначала создать и протестировать образ локально, как показано в следующих командах. Такой подход упрощает тестирование и дополнительную настройку образа перед первым развертыванием в ACR. Однако для этого необходимо установить Docker CLI и убедиться, что управляющая программа Docker запущена.

Сборка образа:

```bash
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Локальный запуск образа:

```bash
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Теперь вы можете открыть приложение по адресу `http://localhost:8080`.

Войдите в Реестр контейнеров Azure:

```azurecli
az acr login -n ${MY_ACR}
```

Передача образа в Реестр контейнеров Azure:

```bash
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Более подробные сведения о создании и хранении образов контейнеров в Azure см. в модуле [Создание и хранение образов контейнеров с помощью службы "Реестр контейнеров Azure"](/learn/modules/build-and-store-container-images/).

Если вы использовали наш [GitHub-репозиторий для быстрого начала работы с контейнерами Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), можете включить в развертывание настраиваемое хранилище ключей, которое добавляется в виртуальную машину Java при запуске. Оно будет добавляться автоматически, если вы разместите файл хранилища ключей в каталог */opt/spring-boot/mycert.crt* . Для этого файл можно добавить непосредственно в Dockerfile или применить поставщик Azure Key Vault для драйвера CSI хранилища секретов, как упоминалось ранее.

Если вы использовали наш [GitHub-репозиторий для быстрого начала работы с контейнерами Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), можете также включить Application Insights, указав переменную среды `APPLICATIONINSIGHTS_CONNECTION_STRING` в файле развертывания Kubernetes со следующим значением: `InstrumentationKey=00000000-0000-0000-0000-000000000000`. Дополнительные сведения см. в статье [Мониторинг приложений Java без написания кода (Azure Monitor Application Insights)](/azure/azure-monitor/app/java-in-process-agent).

Если вам не требуется настраивать образ Docker, можно изучить применение [подключаемого модуля Maven Jib](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) или развертывание в AKS. Дополнительные сведения см. в статье [Развертывание приложения Spring Boot Application в Службе Azure Kubernetes](../spring-framework/deploy-spring-boot-java-app-on-kubernetes.md).

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

### <a name="deploy-to-aks"></a>Развертывание в AKS

Создайте и примените файлы YAML Kubernetes. Дополнительные сведения см. в [кратком руководстве Разверните кластер Службы Azure Kubernetes с помощью Azure CLI](/azure/aks/kubernetes-walkthrough#run-the-application). Если вы создаете внешний балансировщик нагрузки для приложения и контроллера входящего трафика, обязательно укажите IP-адрес (см. предыдущий раздел) в качестве `LoadBalancerIP`.

Включите внешние параметры в качестве переменных среды. См. сведения об [определении переменных среды для контейнера](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/).

Не забудьте включить параметры памяти и ЦП при создании YAML развертывания, чтобы контейнеры имели правильный размер.

### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Настройка записи журналов в консоль и параметров диагностики

Настройте приложения так, чтобы все они выводили журналы в консоль, а не в файлы.

После развертывания приложения в Службе Kubernetes Azure вы сможете просматривать журналы с помощью `kubectl`.

#### <a name="logstashelk-stack"></a>Стек LogStash/ELK

Если вы используете стек LogStash/ELK для объединения журналов, настройте параметр диагностики для потоковой передачи выходных данных консоли в [концентратор событий Azure](/azure/event-hubs/). Затем настройте [подключаемый модуль LogStash EventHub](https://github.com/logstash-plugins/logstash-input-azure_event_hubs) для приема регистрируемых событий в LogStash.

#### <a name="splunk"></a>Splunk

Если вы используете стек Splunk для объединения журналов, настройте потоковый вывод диагностики для передачи выходных данных консоли в [хранилище BLOB-объектов Azure](/azure/storage/blobs/). Затем примените [надстройку Splunk для облачных служб (Майкрософт)](https://splunkbase.splunk.com/app/3757/) для приема событий в Splunk.

### <a name="migrate-and-enable-the-identity-provider"></a>Миграция и включение поставщика удостоверений

Если какому-то из приложений Spring Boot требуется проверка подлинности или авторизация, не забудьте настроить для них доступ к поставщику удостоверений:

* Если роль поставщика удостоверений выполняет Azure Active Directory, дополнительные изменения не требуются.
* Если роль поставщика удостоверений выполняет локальный лес Active Directory, примените решение гибридной идентификации на основе Azure Active Directory. Инструкции для этого есть в [документации по гибридной идентификации](/azure/active-directory/hybrid/).
* Если роль поставщика удостоверений выполняет другое локальное решение, например PingFederate, обратитесь к статье [Выборочная установка Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), чтобы настроить федерацию с Azure Active Directory. Кроме того, вы можете применить Spring Security для работы с поставщиком удостоверений через [OAuth2 и OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) или [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="configure-persistent-storage"></a>Настройка постоянного хранилища

Если для работы приложения требуется энергонезависимое хранилище, настройте один или несколько [постоянных томов](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в AKS нужно убедиться, что оно работает правильно. Выполнив проверку, можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

### <a name="recommendations"></a>Рекомендации

* Мы рекомендуем добавить DNS-имя к IP-адресу, выделенному для контроллера входящего трафика или балансировщика нагрузки приложения. Дополнительные сведения см. в статье о [создании контроллера входящего трафика со статическим общедоступным IP-адресом в AKS](/azure/aks/ingress-static-ip).

* Вы также можете [добавить диаграммы Helm для приложения](https://helm.sh/docs/topics/charts/). Чарты Helm позволяют параметризировать развертывание приложения для использования и настройки различными клиентами.

* Разработайте и реализуйте стратегию DevOps. Чтобы обеспечить надежность и ускорить разработку, вы можете автоматизировать развертывание и тестирование с помощью Azure Pipelines. Дополнительные сведения см. в разделе [Сборка и развертывание в AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Включите [мониторинг Azure для кластера](/azure/azure-monitor/insights/container-insights-enable-existing-clusters), чтобы использовать сбор журналов контейнеров, отслеживание использования и т. д.

* Вы можете предоставить метрики конкретного приложения через Prometheus. Prometheus — это платформа метрик с открытым кодом, широко используемая в сообществе Kubernetes. Вы можете настроить [метрики Prometheus в Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration), чтобы не размещать собственный сервер Prometheus. Так вы включите агрегирование метрик из приложений и автоматическое реагирование или эскалацию при возникновении аномальных условий.

* Разработайте и реализуйте стратегии обеспечения непрерывности бизнес-процессов и аварийного восстановления. Для критически важных приложений вы можете использовать [архитектуру развертывания с несколькими регионами](/azure/aks/operator-best-practices-multi-region).

* Ознакомьтесь с [политикой поддержки версий Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). Вы должны [обновлять кластер AKS](/azure/aks/upgrade-cluster) самостоятельно, чтобы обеспечить его выполнение под управлением поддерживаемой версии.

* Предоставьте всем участникам группы, отвечающей за администрирование кластера и разработку приложений, доступ к соответствующим [рекомендациям по работе с AKS](/azure/aks/best-practices).

* Убедитесь, что в файле развертывания указано, как выполняются последовательные обновления. Дополнительные сведения см. в статье [Rolling Update Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment) (Развертывание последовательного обновления) в документации по Kubernetes.

* Настройте автоматическое масштабирование для учета пиковой загрузки. Дополнительные сведения см. в разделе [Автоматическое масштабирование кластера в соответствии с требованиями приложения в Службе контейнеров Azure](/azure/aks/cluster-autoscaler).

* Для дальнейшей оптимизации производительности вы можете [отслеживать размер кэша кода](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) и добавить параметры `-XX:InitialCodeCacheSize` и `-XX:ReservedCodeCacheSize` в переменную `JAVA_OPTS` в Dockerfile.
