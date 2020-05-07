---
title: Миграция приложений WebLogic в Виртуальные машины Azure
description: Узнайте о том, что следует учитывать при миграции существующего приложения WebLogic для выполнения в Виртуальных машинах Azure.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 10edb96e4e0781945da85d5a872b14178db3122f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673520"
---
# <a name="migrate-weblogic-applications-to-azure-virtual-machines"></a>Миграция приложений WebLogic в Виртуальные машины Azure

Узнайте о том, что следует учитывать при миграции существующего приложения WebLogic для выполнения в Виртуальных машинах Azure.

## <a name="pre-migration"></a>Подготовка к миграции

### <a name="define-what-you-mean-by-migration-complete"></a>Определение целевого состояние после миграции

Это руководство и соответствующие предложения в Azure Marketplace помогут вам без усилий выполнить миграцию рабочих нагрузок WebLogic Server в Azure. Перед выполнением этой процедуры важно все продумать. Возможно, вы хотите перенести существующую инфраструктуру в Виртуальные машины Azure по методике lift-and-shift? В таком случае всегда есть соблазн "улучшить" процедуру.

Но мы рекомендуем как можно точнее соблюдать принцип lift-and-shift, применяя описанные в этом руководстве требуемые изменения. Определите для себя целевое состояние миграции, чтобы вы точно знали, что достигли поставленной задачи. Достигнув целевого состояния, [создайте моментальный снимок Виртуальных машин](/azure/virtual-machines/windows/snapshot-copy-managed-disk). Проверив возможность восстановления из этого моментального снимка, вы сможете вносить улучшения, не боясь, что вы не сможете продолжить миграцию.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>Определение того, можно ли использовать готовые предложения Marketplace в качестве отправной точки

Oracle и Майкрософт совместно работают над созданием в Azure Marketplace набора шаблонов для решений Azure, с помощью которых можно начать миграцию в Azure. См. документацию по [Oracle Fusion Middleware](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/), чтобы изучить список предложений и выбрать то, которое в наибольшей степени отвечает требованиям существующего развертывания. См. список предложений в [документации Oracle](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/select-required-oracle-weblogic-server-offer-azure-marketplace.html#GUID-187739C5-EE7A-47C6-B3BA-C0A0333DC398).

Если ни одно из существующих предложений не подходит в качестве отправной точки, развертывание нужно будет воспроизвести вручную на основе ресурсов Виртуальных машин Azure. См. сведения об [IaaS](https://azure.microsoft.com/overview/what-is-iaas/).

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>Определение совместимости с версией WebLogic

Текущая версия WebLogic должна быть совместимой с версией в предложениях IaaS. Этот запрос отображает предложения для [версии WebLogic 12.2.1.3](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1). Если текущая версия WebLogic не совместима с этой версией, вам нужно будет вручную воспроизвести развертывание на основе ресурсов IaaS Azure. См. сведения в [документации по Azure](https://azure.microsoft.com/overview/what-is-iaas/).

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly](includes/validate-that-the-supported-java-version-works-correctly.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

[!INCLUDE [domain-configuration](includes/domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/determine-whether-wlst-is-used.md)]

[!INCLUDE [validate-whether-and-how-the-file-system-is-used](includes/validate-whether-and-how-the-file-system-is-used.md)]

[!INCLUDE [determine-the-network-topology](includes/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>Миграция

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Выбор предложения WebLogic в Виртуальных машинах Azure

Для WebLogic доступны следующие предложения в Виртуальных машинах Azure.

Во время развертывания предложения вам будет предложено выбрать размер виртуальной машины для узлов WebLogic Server. При выборе размера виртуальной машины важно учитывать все аспекты (память, процессор, диск). См. сведения см. в документации по [предложениям](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/deploy-oracle-weblogic-server-administration-server-single-node.html) и [размерам виртуальных машин Azure](/azure/cloud-services/cloud-services-sizes-specs).

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>Отдельный узел WebLogic Server без сервера администрирования

Это предложение создает одну виртуальную машину и устанавливает на ней WebLogic, но не настраивает домены. Такой подход используется в сценариях с высокой степенью персонализации в конфигурации домена.

#### <a name="weblogic-server-single-node-with-admin-server"></a>Отдельный узел WebLogic Server с сервером администрирования

Это предложение подготавливает одну виртуальную машину и устанавливает на ней WebLogic Server 12.1.2.3. При этом создается домен и запускается сервер администрирования.

#### <a name="weblogic-server-n-node-cluster"></a>N-узловой кластер WebLogic Server

Это предложение создает высокодоступный кластер виртуальных машин WebLogic Server.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>N-узловой динамический кластер WebLogic Server

Это предложение создает высокодоступный масштабируемый динамический кластер виртуальных машин WebLogic Server.

### <a name="provision-the-offer"></a>Подготовка предложения

Выбрав предложение в качестве отправной точки, подготовьте его согласно [этой документации](https://wls-eng.github.io/arm-oraclelinux-wls/). Учтите, что здесь нужно выбрать доменное имя, которое вы уже используете. Вы даже можете указать существующий пароль домена.

### <a name="migrate-the-domains"></a>Миграция доменов

Подготовив предложение, изучите конфигурацию доменов и изучите инструкции по [миграции доменов](https://support.oracle.com/knowledge/Middleware/2336356_1.html).

### <a name="connect-the-databases"></a>Подключение баз данных

Перенеся домены, вы можете подключить базы данных по инструкциям из [этой документации](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster). Эти инструкции предусматривают использование всех соответствующих секретов и строк доступа для баз данных.

### <a name="account-for-keystores"></a>Учет хранилищ ключей

При миграции следует учитывать все хранилища ключей SSL, которые используются приложением. См. сведения о том, как [настроить хранилище ключей](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC).

### <a name="connect-the-jms-sources"></a>Подключение источников JMS

Подключившись к базам данных, вы можете настроить JMS согласно руководству по [администрированию ресурсов JMS для сервера Oracle WebLogic Server с помощью Fusion Middleware](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm).

### <a name="account-for-logging"></a>Учетная запись для ведения журнала

При миграции домена необходимо перенести существующую конфигурацию ведения журналов. См. сведения о том, как настроить [уровни ведения журнала в java.util.logging](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlach/taskhelp/logging/ConfigureJavaLoggingLevels.html), а также [файлы журналов и фильтрацию сообщений журнала для Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wllog/index.html).

### <a name="migrating-your-applications"></a>Миграция приложений

Методики, которые группы разработки используют для развертывания приложений на тестовых, промежуточных и рабочих серверах, могут сильно отличаться. В некоторых случаях используется оптимизированная платформа CI/CD, настроенная для развертывания приложений на сервере WebLogic Server. В других случаях процесс частично может выполняться вручную. Одним из преимуществ использования Виртуальных машин Azure для миграции приложений WebLogic в облако является то, что выполнение существующих процессов не будет прерываться.

Для группы безопасности сети, которую подготовило предложение, вам нужно настроить доступ из конвейера CI/CD или системы развертывания, выполняемого вручную. См. сведения о [группах безопасности Azure](/azure/virtual-network/security-overview).

### <a name="testing"></a>Тестирование

При тестировании приложений в контейнере должны быть доступными новые серверы, работающие в Azure. Как и для CI/CD, здесь важно настроить правила безопасности сети, открывающие доступ к приложениям, развернутым в Azure, для тестирования. См. сведения о [группах безопасности Azure](/azure/virtual-network/security-overview).

## <a name="post-migration"></a>Действия после миграции

После достижения целевого состояния миграции, которое вы определили на этапе [подготовки к миграции](#pre-migration), выполните комплексное приемочное тестирование и убедитесь, что все работает правильно. После миграции вы можете дополнительно усовершенствовать систему. Это можно сделать следующим образом:

* Используйте службу хранилища Azure для обслуживания статического содержимого, подключенного к виртуальным машинам. См. сведения о том, как [подключать диск данных к виртуальной машине и отключать его от нее](/azure/lab-services/devtest-lab-attach-detach-data-disk).

* Разверните приложения в перенесенный кластер WebLogic с помощью Azure DevOps. См. сведения о том, как [начать работу с Azure DevOps](/azure/devops/get-started/?view=azure-devops).

* Включите в топологию сети расширенные службы балансировки нагрузки. См. сведения о том, как [использовать службы балансировки нагрузки в Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure).

* Используйте управляемые удостоверения Azure для управления секретами и настройте доступ к ресурсам Azure на основе ролей. См. сведения об [управляемых удостоверениях для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview).

* Интегрируйте проверку подлинности и авторизацию WebLogic Java EE с использованием Azure Active Directory. См. сведения о том, как [выполнить интеграцию Azure Active Directory](/azure/active-directory/manage-apps/plan-an-application-integration).

* Используйте Azure Key Vault для хранения любых сведений, которые считаются секретными. См. [основные понятия Azure Key Vault](/azure/key-vault/basic-concepts).
