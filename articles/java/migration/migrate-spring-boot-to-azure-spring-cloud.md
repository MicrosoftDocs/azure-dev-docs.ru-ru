---
title: Перенос приложений Spring Boot в Azure Spring Cloud
description: В этом руководстве описано, что следует учитывать при переносе существующего приложения Spring Boot для выполнения в Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4d2f84c6c77294c9a2d25028608e2feb712599f8
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062067"
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

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Действия после миграции

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
