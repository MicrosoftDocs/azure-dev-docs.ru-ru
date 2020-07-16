---
title: Перенос приложений WebSphere в WildFly в Службе Azure Kubernetes
description: Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebSphere для запуска в WildFly в контейнере Службы Azure Kubernetes.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: 44e7b2a88a66fb837b6ff9d11c2a534d921d8155
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379718"
---
# <a name="migrate-websphere-applications-to-wildfly-on-azure-kubernetes-service"></a>Перенос приложений WebSphere в WildFly в Службе Azure Kubernetes

Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebSphere для запуска в WildFly в контейнере Службы Azure Kubernetes.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Проверка всех секретов

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретов и паролей. Обязательно проверьте наличие файла *ibm-web-bnd.xml* в WAR-файлах. Кроме того, в приложении могут быть файлы конфигурации, содержащие пароли или учетные данные.

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>Проверка ресурсов JNDI

Проверьте все ресурсы JNDI. Для других ресурсов, таких как брокеры сообщений JMS, может потребоваться выполнить миграцию или перенастройку.

#### <a name="inside-your-application"></a>В приложении

Проверьте файл *WEB-INF/ibm-web-bnd.xml* и (или) *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Определение источников данных

Если приложение использует какие-либо базы данных, необходимо определить следующие сведения:

* имя источника данных;
* конфигурация пула подключений;
* путь к JAR-файлу драйвера JDBC.

Дополнительные сведения см. в разделе о [настройке подключения к базе данных](https://www.ibm.com/support/knowledgecenter/SSQP76_8.10.x/com.ibm.odm.distrib.config.was/config_dc_websphere/tpc_was_create_datasrc_cpl.html) в документации по WebSphere.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Для использования файловой системы на сервере приложений требуется перенастройка или, в редких случаях, изменение архитектуры. Файловая система может использоваться модулями WebSphere или кодом приложения. Вы можете определить некоторые или все сценарии, описанные в следующих разделах.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

### <a name="determine-whether-your-application-uses-websphere-specific-apis"></a>Определение того, использует ли приложение API, зависящие от WebSphere

Если приложение использует API, зависящие от WebSphere, необходимо выполнить рефакторинг для удаления этих зависимостей. Например, если вы применили класс, упомянутый в [спецификации по API, IBM WebSphere Application Server (выпуск 9.0)](https://www.ibm.com/support/knowledgecenter/en/SSEQTJ_9.0.5/com.ibm.websphere.javadoc.doc/web/apidocs/overview-summary.html?view=embed), значит вы использовали API WebSphere в приложении.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Определение того, используются ли соединители JCA

Если приложение использует соединители JCA, нужно проверить, можно ли использовать такой соединитель в WildFly. Если же реализация JCA связана с WebSphere, необходимо выполнить рефакторинг приложения, чтобы удалить эту зависимость. Если это возможно, добавьте JAR в путь к классу сервера и поместите необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly, чтобы обеспечить доступность.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Определение того, упаковано ли приложение как EAR-файл

Если приложение упаковано как EAR-файл, обязательно проверьте файлы *application.xml* и *application-bnd.xml*, определив их конфигурации.

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
