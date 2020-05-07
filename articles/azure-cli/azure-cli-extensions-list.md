---
title: Доступные расширения для Azure CLI
description: Полный список официально поддерживаемых расширений для Azure CLI.
author: haroldrandom
ms.author: jianzen
manager: yonzhan,yungezz
ms.date: 04/16/2020
ms.topic: article
ms.prod: azure
ms.technology: azure-cli
ms.devlang: azure-cli
ms.openlocfilehash: 531ea056b276cc12d5807ed62baa4d3c6044c26e
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030956"
---
# <a name="available-extensions-for-the-azure-cli"></a>Доступные расширения для Azure CLI

В этой статье приведен полный список доступных расширений для Azure CLI, которые поддерживает корпорация Майкрософт.

Список расширений также доступен из CLI. Для этого выполните команду [az extension list-available](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available):

```azurecli-interactive
az extension list-available --output table
```

| Имя | Версия | Сводка | Preview (Предварительный просмотр) |
|------|---------|---------|---------|
| [aem](https://github.com/Azure/azure-cli-extensions) | 0.1.1 | Управление расширениями для улучшенного мониторинга Azure для SAP. |  |
| [ai-examples](https://github.com/Azure/azure-cli-extensions/tree/master/src/ai-examples) | 0.2.0 | Добавление примеров на основе искусственного интеллекта в содержимое справки. | Да |
| [aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) | 0.4.43 | Предварительное знакомство с будущими возможностями AKS. | Да |
| [alertsmanagement](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение оповещений для программ командной строки Microsoft Azure |  |
| [alias](https://github.com/Azure/azure-cli-extensions) | 0.5.2 | Поддержка псевдонимов команд. | Да |
| [application-insights](https://github.com/Azure/azure-cli-extensions/tree/master/src/application-insights) | 0.1.6 | Поддержка компонентов управления Application Insights, а также запрашивание метрик, событий и журналов из таких компонентов. | Да |
| [azure-batch-cli-extensions](https://github.com/Azure/azure-batch-cli-extensions) | 5.0.1 | Дополнительные команды для работы с пакетной службой Azure. |  |
| [azure-cli-iot-ext](https://github.com/azure/azure-iot-cli-extension) | 0.8.9 | Расширение Azure IoT для Azure CLI. |  |
| [azure-cli-ml](https://docs.microsoft.com/azure/machine-learning/service/) | 1.3.0 | Командный модуль средств командной строки Azure ML в Microsoft Azure |  |
| [azure-devops](https://github.com/Microsoft/azure-devops-cli-extension) | 0.18.0 | Инструменты для управления Azure DevOps. |  |
| [azure-firewall](https://github.com/Azure/azure-cli-extensions/tree/master/src/azure-firewall) | 0.3.1 | Управление ресурсами Брандмауэра Azure. | Да |
| [azure-iot](https://github.com/azure/azure-iot-cli-extension) | 0.9.1 | Расширение Azure IoT для Azure CLI. |  |
| [blueprint](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение Blueprint для программ командной строки Microsoft Azure |  |
| [connectedmachine](https://github.com/Azure/azure-cli-extensions) | 0.1.1 | Расширение Connectedmachine программ командной строки Microsoft Azure | Да |
| [connection-monitor-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/connection-monitor-preview) | 0.1.0 | Расширение Connection Monitor для командной строки Microsoft Azure (версия 2) | Да |
| [csvmware](https://github.com/Azure/az-vmware-cli) | 0.3.0 | Управление решением VMware в Azure от CloudSimple. | Да |
| [databox](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение Data Box для программ командной строки Microsoft Azure |  |
| [databricks](https://github.com/Azure/azure-cli-extensions) | 0.2.0 | Расширение DatabricksClient для программ командной строки Microsoft Azure |  |
| [db-up](https://github.com/Azure/azure-cli-extensions/tree/master/src/db-up) | 0.1.13 | Дополнительные команды для упрощения рабочих процессов Базы данных Azure. | Да |
| [deploy-to-azure](https://github.com/Azure/deploy-to-azure-cli-extension) | 0.2.0 | Развертывание в Azure с помощью действий GitHub. | Да |
| [dev-spaces](https://github.com/Azure/azure-cli-extensions) | 1.0.5 | Рабочие среды Azure для разработчиков предоставляют быстрый итеративный интерфейс разработки Kubernetes для команд. |  |
| [dev-spaces-preview](https://github.com/Azure/azure-cli-extensions) | 0.1.6 | Рабочие среды Azure для разработчиков предоставляют быстрый итеративный интерфейс разработки Kubernetes для команд. | Да |
| [dms-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/dms-preview) | 0.11.0 | Поддержка новых сценариев Database Migration Service. | Да |
| [eventgrid](https://github.com/Azure/azure-cli-extensions) | 0.4.7 | Командный модуль средств командной строки Сетки событий в Microsoft Azure. | Да |
| [express-route](https://github.com/Azure/azure-cli-extensions/tree/master/src/express-route) | 0.1.3 | Управление ExpressRoute с помощью предварительных версий функций. | Да |
| [express-route-cross-connection](https://github.com/Azure/azure-cli-extensions/tree/master/src/express-route-cross-connection) | 0.1.1 | Управление пользовательскими каналами ExpressRoute с использованием перекрестного подключения ExpressRoute. |  |
| [front-door](https://github.com/Azure/azure-cli-extensions/tree/master/src/front-door) | 1.0.6 | Управление сетевыми службами Front Door. |  |
| [hack](https://github.com/Azure/azure-cli-extensions) | 0.4.2 | Расширение программ командной строки Microsoft Azure для взлома | Да |
| [healthcareapis](https://github.com/Azure/azure-cli-extensions) | 0.1.3 | Расширение программы командной строки Microsoft Azure HealthCareApis |  |
| [hpc-cache](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение кэша хранилища для программ командной строки Microsoft Azure | Да |
| [image-copy-extension](https://github.com/Azure/azure-cli-extensions) | 0.2.3 | Поддержка копирования управляемых образов виртуальных машин в другие регионы |  |
| [интерактивный](https://github.com/Azure/azure-cli) | 0.4.4 | Интерактивная оболочка командной строки Microsoft Azure | Да |
| [internet-analyzer](https://github.com/Azure/azure-cli-extensions) | 0.1.0rc5 | Расширение Internet Analyzer программ командной строки Microsoft Azure | Да |
| [ip-group](https://github.com/Azure/azure-cli-extensions) | 0.1.1 | Расширение IpGroup программ командной строки Microsoft Azure |  |
| [keyvault-preview](https://github.com/Azure/azure-keyvault-cli-extension) | 0.1.3 | Команды предварительной версии Azure Key Vault. | Да |
| [log-analytics](https://github.com/Azure/azure-cli-extensions/tree/master/src/log-analytics) | 0.1.4 | Поддержка возможностей запросов Azure Log Analytics. | Да |
| [maintenance](https://github.com/Azure/azure-cli-extensions) | 1.0.1 | Поддержка управления обслуживанием Azure. |  |
| [managementpartner](https://github.com/Azure/azure-cli-extensions) | 0.1.2 | Поддержка партнера управления (предварительная версия). |  |
| [mesh](https://github.com/Azure/azure-cli-extensions) | 0.10.6 | Поддержка Сетки Microsoft Azure Service Fabric (общедоступная предварительная версия) | Да |
| [mixed-reality](https://github.com/Azure/azure-cli-extensions) | 0.0.1 | Расширение Azure CLI смешанной реальности. |  |
| [netappfiles-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/netappfiles-preview) | 0.3.2 | Предварительное знакомство с новыми возможностями Azure NetApp Files (ANF). | Да |
| [notification-hub](https://github.com/Azure/azure-cli-extensions) | 0.2.0 | Расширение Notification Hub для программ командной строки Microsoft Azure |  |
| [peering](https://github.com/Azure/azure-cli-extensions) | 0.1.0rc2 | Расширение Peering программ командной строки Microsoft Azure | Да |
| [powerbidedicated](https://github.com/Azure/azure-cli-extensions) | 0.1.1 | Расширение PowerBIDedicated для программ командной строки Microsoft Azure | Да |
| [privatedns](https://github.com/Azure/azure-cli-extensions) | 0.1.1 | Команды для управления Частными зонами DNS. | Да |
| [resource-graph](https://github.com/Azure/azure-cli-extensions/tree/master/src/resource-graph) | 1.1.0 | Поддержка запросов к ресурсам Azure с помощью Resource Graph. | Да |
| [sap-hana](https://github.com/Azure/azure-hanaonazure-cli-extension) | 0.5.9 | Дополнительные команды для работы с экземплярами SAP HanaOnAzure. |  |
| [spring-cloud](https://github.com/Azure/azure-cli-extensions) | 0.2.2 | Расширение spring-cloud программ командной строки Microsoft Azure | Да |
| [storage-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview) | 0.2.10 | Предварительная версия ожидаемых функций хранилища. | Да |
| [storagesync](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение синхронизации службы хранилища Майкрософт для программ командной строки Microsoft Azure |  |
| [stream-analytics](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение Stream Analytics для программ командной строки Microsoft Azure |  |
| [subscription](https://github.com/Azure/azure-cli-extensions) | 0.1.3 | Поддержка управления подписками (предварительная версия). |  |
| [support](https://github.com/azure/azure-cli-extensions/tree/master/src/support) | 1.0.1 | Расширение Support для программ командной строки Microsoft Azure |  |
| [synapse](https://github.com/Azure/azure-cli-extensions) | 0.1.0 | Расширение Synapse для программ командной строки Microsoft Azure | Да |
| [virtual-network-tap](https://github.com/Azure/azure-cli-extensions/tree/master/src/virtual-network-tap) | 0.1.0 | Управление точками доступа к виртуальным сетям (VTAP). | Да |
| [virtual-wan](https://github.com/Azure/azure-cli-extensions/tree/master/src/virtual-wan) | 0.1.3 | Управление виртуальными глобальными сетями, концентраторами, шлюзами и сайтами VPN. | Да |
| [vm-repair](https://github.com/Azure/azure-cli-extensions/tree/master/src/vm-repair) | 0.2.6 | Команды автоматического восстановления для исправления виртуальных машин. |  |
| [vmware](https://github.com/virtustream/azure-vmware-virtustream-cli-extension) | 0.5.5 | Предварительная версия решения Azure VMware от Virtustream. | Да |
| [webapp](https://github.com/Azure/azure-cli-extensions/tree/master/src/webapp) | 0.2.24 | Дополнительные команды для Службы приложений Azure. | Да |