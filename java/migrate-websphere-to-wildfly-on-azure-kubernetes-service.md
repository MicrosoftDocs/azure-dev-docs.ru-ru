---
title: Перенос приложений WebSphere в WildFly в Службе Azure Kubernetes
description: Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebSphere для запуска в WildFly в контейнере Службы Azure Kubernetes.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.openlocfilehash: a7cffbef718fb90241cfbd68de8525192644730b
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897357"
---
# <a name="migrate-websphere-applications-to-wildfly-on-azure-kubernetes-service"></a>Перенос приложений WebSphere в WildFly в Службе Azure Kubernetes

Из этого руководства вы узнаете, что следует учитывать при переносе приложения WebSphere для запуска в WildFly в контейнере Службы Azure Kubernetes.

## <a name="pre-migration"></a>Подготовка к миграции

[!INCLUDE [inventory-server-capacity-aks](includes/migration/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Проверка всех секретов

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретов и паролей. Обязательно проверьте наличие файла *ibm-web-bnd.xml* в WAR-файлах. Кроме того, в приложении могут быть файлы конфигурации, содержащие пароли или учетные данные.

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Проверка правильной работы поддерживаемой версии Java

Для использования WildFly в Службе Azure Kubernetes требуется определенная версия Java. Поэтому вам нужно проверить, может ли приложение правильно работать с этой поддерживаемой версией. Эта проверка особенно важна, если на текущем сервере используется поддерживаемая версия JDK (например, Oracle JDK или IBM OpenJ9).

Чтобы узнать текущую версию, войдите на сервер в рабочей среде и выполните следующую команду:

```bash
java -version
```

### <a name="inventory-jndi-resources"></a>Проверка ресурсов JNDI

Проверьте все ресурсы JNDI. Для других ресурсов, таких как брокеры сообщений JMS, может потребоваться выполнить миграцию или перенастройку.

#### <a name="inside-your-application"></a>В приложении

Проверьте файл *WEB-INF/ibm-web-bnd.xml* и (или) *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Определение источников данных

Если приложение использует какие-либо базы данных, необходимо определить следующие сведения:

* имя источника данных;
* конфигурация пула подключений;
* путь к JAR-файлу драйвера JDBC.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Определение того, используется ли файловая система и как именно она используется

Для использования файловой системы на сервере приложений требуется перенастройка или, в редких случаях, изменение архитектуры. Файловая система может использоваться модулями WebSphere или кодом приложения. Вы можете определить некоторые или все сценарии, описанные в следующих разделах.

#### <a name="read-only-static-content"></a>Статическое содержимое только для чтения

Если ваше приложение сейчас обслуживает статическое содержимое, вам потребуется альтернативное расположение для этого статического содержимого. Вы можете переместить статическое содержимое в хранилище BLOB-объектов Azure и включить Azure CDN для быстрого скачивания в глобальном масштабе. См. руководство по [размещению статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website) и [ по интеграции учетной записи хранения Azure с Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Динамически опубликованное статическое содержимое

Если приложение допускает использование статического содержимого, которое передается или создается приложением и после этого становится неизменяемым, вы можете использовать хранилище BLOB-объектов Azure и Azure CDN, как описано выше, с Функциями Azure для выполнения отправки и обновления CDN. Практический пример реализации см. в руководстве по [отправке и предварительной загрузке статического содержимого CDN с помощью Функций Azure](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="dynamic-or-internal-content"></a>Динамическое или внутреннее содержимое

Для использования файлов, которые часто записываются и считываются приложением (например, временные файлы данных), или статических файлов, видимых только для вашего приложения, вы можете подключить общие папки службы хранилища Azure в качестве постоянных томов. См. сведения о [динамическом создании и использовании постоянного тома с Файлами Azure в Службе Azure Kubernetes](/azure/aks/azure-files-dynamic-pv).

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/migration/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/migration/determine-whether-jms-queues-or-topics-are-in-use.md)]

### <a name="determine-whether-your-application-uses-websphere-specific-apis"></a>Определение того, использует ли приложение API, зависящие от WebSphere

Если приложение использует API, зависящие от WebSphere, необходимо выполнить рефакторинг для удаления этих зависимостей. Например, если вы применили класс, упомянутый в [спецификации по API, IBM WebSphere Application Server (выпуск 9.0)](https://www.ibm.com/support/knowledgecenter/en/SSEQTJ_9.0.5/com.ibm.websphere.javadoc.doc/web/apidocs/overview-summary.html?view=embed), значит вы использовали API WebSphere в приложении.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/migration/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/migration/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/migration/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Определение того, используются ли соединители JCA

Если приложение использует соединители JCA, нужно проверить, можно ли использовать такой соединитель в WildFly. Если же реализация JCA связана с WebSphere, необходимо выполнить рефакторинг приложения, чтобы удалить эту зависимость. Если это возможно, добавьте JAR в путь к классу сервера и поместите необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly, чтобы обеспечить доступность.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/migration/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/migration/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Определение того, упаковано ли приложение как EAR-файл

Если приложение упаковано как EAR-файл, обязательно проверьте файлы *application.xml* и *application-bnd.xml*, определив их конфигурации.

> [!NOTE]
> Если нужно масштабировать каждое веб-приложение отдельно для эффективного использования ресурсов AKS, следует разбить EAR на отдельные веб-приложения.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [perform-in-place-testing](includes/migration/perform-in-place-testing.md)]

## <a name="migration"></a>Миграция

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/migration/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/migration/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/migration/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/migration/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/migration/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Настройка постоянного хранилища

Если для работы приложения требуется энергонезависимое хранилище, настройте один или несколько [постоянных томов](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migration/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в Azure Kubernetes Service нужно убедиться, что оно работает правильно. Выполнив проверку, можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

[!INCLUDE [recommendations-wildfly-on-aks](includes/migration/recommendations-wildfly-on-aks.md)]
