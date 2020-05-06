---
title: Таблица версий и модулей Ansible для Azure| Документация Майкрософт
description: Таблица версий и модулей Ansible для Azure
keywords: ansible, roles, matrix, version, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "80741652"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают в себя виртуальные машины, масштабируемые наборы, сетевые службы и службы контейнеров. В этой статье описаны различные модули Ansible для Azure и версии Ansible, в которые они входят.

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure

Эти модули могут выполняться прямо на удаленных узлах или с помощью сборников схем.  

Эти модули доступны в официальном выпуске Ansible и в следующих ролях сборниках схем от Майкрософт.

> [!NOTE]
> Начиная с Ansible 2.9, мы переименовали все модули *_facts в *_info, чтобы обеспечить соответствие соглашению Ansible об именовании. Старые и переименованные модули связаны и работают, как и раньше (помимо отображения предупреждения о прекращении поддержки).

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Среда выполнения приложений**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_availabilityset_info              | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_deployment                         | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_functionapp                        | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_functionapp_info                  | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_image                              | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_resource                           | -            | -                           | Да          | Да          | Да          | Да          | Да          |
| azure_rm_resource_info                     | -            | -                           | Да          | Да          | Да          | Да          | Да          |
| azure_rm_resourcegroup                      | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_resourcegroup_info                | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_virtualmachine                     | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachineextension            | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_virtualmachineimage_info          | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescaleset             | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescaleset_info       | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Да          | Да          | Да          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Да          | Да          | Да          |
| **Сеть**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_dnsrecordset                       | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_dnsrecordset_info                 | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_dnszone                            | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_dnszone_info                      | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_loadbalancer                       | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_loadbalancer_info                 | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_networkinterface                   | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_networkinterface_info             | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_publicipaddress                    | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_publicipaddress_info              | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_route                              | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_routetable                         | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_routetable_info                   | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_securitygroup                      | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_subnet                             | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_virtualnetwork                     | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualnetwork_info               | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Да          | Да          | Да          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Да          | Да          |
| **Память**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_manageddisk_info                  | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_storageaccount                     | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_storageaccount_info               | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_storageblob                        | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_webapp                             | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_webapp_info                       | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Да          | Да          | Да          |
| **Контейнеры**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Да          | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_aks                                | -            | -                           | Да          | Да          | Да          | Да          | Да          |
| azure_rm_aks_info                          | -            | -                           | Да          | Да          | Да          | Да          | Да          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_containerinstance                  | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_containerregistry                  | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Да          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Да          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Да          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Да          |
| **Базы данных**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mysqldatabase                      | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_mysqlserver                        | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_postgresqldatabase                 | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_postgresqlserver                   | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_sqldatabase                        | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Да          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Да          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Да          | Да          | Да          | Да          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_sqlserver                          | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_sqlserver_info                    | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Да          | Да          |
| **Интеграция**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Да          | Да          | Да          |
| **Безопасность**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Да          | Да          | Да          |
| azure_rm_keyvaultkey                        | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_keyvaultsecret                     | -            | Да                         | Да          | Да          | Да          | Да          | Да          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Да          | Да          | Да          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Да          | Да          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Да          | Да          | Да          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Да          | Да          | Да          | Да          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Да          | Да          | Да          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Да          | Да          |
| **Управление**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Да        | Да          |
| **Интернет вещей**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Да        | Да          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Да        | Да          |

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure

[Роль azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для подготовки ресурсов Azure, рекомендуем установить роль сборника схем `azure_preview_module`.

Роль сборника схем `azure_preview_module` выпускается каждые три недели.

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о [создании повторно используемых сборников схем](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 