---
title: Перенос приложений WebLogic в WildFly в Службе Azure Kubernetes
description: Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebLogic для запуска в WildFly в контейнере Службы Azure Kubernetes.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: aa496125a5a692f13e90441eb7be85f049481151
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379778"
---
# <a name="migrate-weblogic-applications-to-wildfly-on-azure-kubernetes-service"></a>Перенос приложений WebLogic в WildFly в Службе Azure Kubernetes

Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebLogic для запуска в WildFly в контейнере Службы Azure Kubernetes.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь с дополнительным руководством по переносу:

* [Миграция приложений WebLogic в Виртуальные машины Azure](migrate-weblogic-to-virtual-machines.md)

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

### <a name="determine-whether-session-replication-is-used"></a>Определение того, используется ли репликация сеансов

Если работа приложения зависит от репликации сеансов с помощью модуля Oracle Coherence*Web или без него, у вас есть два варианта:

* Выполните рефакторинг приложения, чтобы использовать базу данных для управления сеансами.
* Выполните рефакторинг приложения, чтобы перенести сеанс в службу Redis Azure. См. сведения на странице с [ценами на Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

<!-- AKS-specific extension of the last INCLUDE. -->
> [!NOTE]
> Если нужно масштабировать каждое веб-приложение отдельно для эффективного использования ресурсов AKS, следует разбить EAR на отдельные веб-приложения.
<!-- end extension -->

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

### <a name="determine-whether-weblogic-scripting-tool-wlst-is-used"></a>Определение того, используется ли WebLogic Scripting Tool (WLST)

Если сейчас вы используете WLST для развертывания, вам нужно определить, какие задачи это средство выполняет. Если WLST изменяет параметры (рабочей среды) для приложения в рамках развертывания, убедитесь, что эти параметры соответствуют одному из следующих условий:

* они извлечены как параметры приложения;
* они внедрены в приложение;
* используется интерфейс командной строки (CLI) JBoss во время развертывания.

Если WLST выполняет больше задач, чем перечислено выше, вам потребуется выполнить еще несколько действий при миграции.

### <a name="determine-whether-your-application-uses-weblogic-specific-apis"></a>Определение того, использует ли приложение API, зависящие от WebLogic

Если приложение использует интерфейсы API, зависящие от WebLogic, необходимо выполнить рефакторинг для удаления этих зависимостей. Например, если вы применили класс, упомянутый в [справочнике по API Java для Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlapi/index.html?overview-summary.html), значит в вашем приложении используется API, зависящий от WebLogic.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

### <a name="determine-whether-a-deployment-plan-was-used"></a>Определение того, использовался ли план развертывания

Если приложение развернуто с помощью плана развертывания, узнайте, какие задачи входят в этот план. Если план развертывания предусматривает простое развертывание, вы можете развернуть веб-приложение без изменений. В случае использования более сложного плана, определите, можно ли применить CLI JBoss для правильной настройки приложения в рамках развертывания. Если CLI JBoss невозможно использовать, выполните рефакторинг приложения таким образом, чтобы необходимость в плане развертывания отпала.

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Для использования файловой системы на сервере приложений требуется перенастройка или, в редких случаях, изменение архитектуры. Файловую систему могут использовать модули WebLogic или код приложения. Вы можете определить некоторые или все сценарии, описанные в следующих разделах.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

### <a name="determine-whether-jca-connectors-are-used"></a>Определение того, используются ли соединители JCA

Если приложение использует соединители JCA, нужно проверить, поддерживается ли использование таких соединителей в WildFly. Если же реализация JCA зависит от WebLogic, необходимо выполнить рефакторинг приложения, чтобы удалить эту зависимость. Если это возможно, добавьте JAR в путь к классу сервера и поместите необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly, чтобы обеспечить доступность.

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

### <a name="determine-whether-weblogic-clustering-is-used"></a>Определение того, используется ли кластеризация WebLogic

Скорее всего, вы развернули приложение на нескольких серверах WebLogic Server для обеспечения высокой доступности. Служба Kubernetes Azure поддерживает масштабирование, но если вы использовали API кластера WebLogic, необходимо выполнить рефакторинг кода, чтобы не использовать этот API.

[!INCLUDE [perform-in-place-testing](includes/perform-in-place-testing.md)]

## <a name="migration"></a>Миграция

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Настройка постоянного хранилища

Если для работы приложения требуется энергонезависимое хранилище, настройте один или несколько [постоянных томов](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в Azure Kubernetes Service нужно убедиться, что оно работает правильно. Когда выполните проверку, просмотрите следующие рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
