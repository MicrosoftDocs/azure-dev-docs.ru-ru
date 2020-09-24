---
title: Перенос приложений JBoss EAP в WildFly в Службе Azure Kubernetes
description: Из этого руководства вы узнаете, что следует учитывать при переносе приложения JBoss EAP для запуска в WildFly в контейнере Службы Azure Kubernetes.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 3/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5de4032f4413f4c72bf51990723c47874bc39ab2
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738572"
---
# <a name="migrate-jboss-eap-applications-to-wildfly-on-azure-kubernetes-service"></a>Перенос приложений JBoss EAP в WildFly в Службе Azure Kubernetes

Из этого руководства вы узнаете, что следует учитывать при переносе приложения JBoss EAP для запуска в WildFly в контейнере Службы Azure Kubernetes.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Проверка всех секретов

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретов и паролей. Обязательно проверьте наличие файла *jboss-web.xml* в WAR-файлах. Кроме того, в приложении могут быть файлы конфигурации, содержащие пароли или учетные данные.

Мы рекомендуем хранить секреты в Azure KeyVault. См. [основные понятия Azure Key Vault](/azure/key-vault/basic-concepts).

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>Проверка ресурсов JNDI

Проверьте все ресурсы JNDI. Для других ресурсов, таких как брокеры сообщений JMS, может потребоваться выполнить миграцию или перенастройку.

### <a name="determine-whether-session-replication-is-used"></a>Определение того, используется ли репликация сеансов

Если приложение использует репликацию сеансов, вам потребуется удалить из него эту зависимость.

#### <a name="inside-your-application"></a>В приложении

Проверьте файлы *WEB-INF/jboss-web.xml* и (или) *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Определение источников данных

Если приложение использует какие-либо базы данных, необходимо определить следующие сведения:

* имя источника данных;
* конфигурация пула подключений;
* путь к JAR-файлу драйвера JDBC.

См. сведения об [источниках данных JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/datasource_management) в документации по JBoss EAP.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Для использования файловой системы на сервере приложений требуется перенастройка или, в редких случаях, изменение архитектуры. Файловая система может использоваться модулями JBoss EAP или кодом приложения. Вы можете определить некоторые или все сценарии, описанные в следующих разделах.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

### <a name="determine-whether-your-application-uses-jboss-eap-specific-apis"></a>Определение того, использует ли приложение API, зависящие от JBoss EAP

Если приложение использует API, зависящие от JBoss EAP, необходимо выполнить рефакторинг для удаления этих зависимостей.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Определение того, используются ли соединители JCA

Если приложение использует соединители JCA, проверьте, можно ли использовать соединитель JCA в WildFly. Если же реализация JCA зависит от JBoss EAP, необходимо выполнить рефакторинг приложения, чтобы удалить эту зависимость. Если вы можете использовать соединитель JCA в WildFly, для его работы нужно добавить файлы JAR в путь к классу сервера и поместить необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Определение того, упаковано ли приложение как EAR-файл

Если приложение упаковано как EAR-файл, обязательно проверьте файл *application.xml*, определив его конфигурацию.

> [!NOTE]
> Если нужно масштабировать каждое веб-приложение отдельно для эффективного использования ресурсов AKS, следует разбить EAR на отдельные веб-приложения.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

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

После переноса приложения в Azure Kubernetes Service нужно убедиться, что оно работает правильно. Выполнив проверку, можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
