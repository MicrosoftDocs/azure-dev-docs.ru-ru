---
title: Заметки о выпуске Azure CLI
description: Узнайте о последних обновлениях в Azure CLI
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 04/21/2020
ms.topic: article
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 10dfdc316ba00f8a7019f0724aab231e344c1c6d
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82031316"
---
# <a name="azure-cli-release-notes"></a>Заметки о выпуске Azure CLI

## <a name="april-21-2020"></a>21 апреля 2020 г.

Версия 2.4.0

### <a name="acr"></a>ACR

* `az acr run --cmd`: отключает переопределение рабочего каталога.
* Включена поддержка выделенной конечной точки данных.

### <a name="aks"></a>AKS

* `az aks list -o table` теперь показывает privateFqdn как FQDN для частных кластеров.
* Добавлен параметр --uptime-sla.
* Обновлен пакет containerservice.
* Включена поддержка общедоступных IP-адресов узлов.
* Исправлена опечатка в команде справки.

### <a name="appconfig"></a>AppConfig

* Разрешена ссылка на хранилище ключей для команд kv list и export.
* Исправлена ошибка при отображении значений ключей.

### <a name="appservice"></a>AppService

* `az functionapp create`: изменен способ настройки linuxFxVersion для приложений-функций dotnet в Linux. Это должно исправить ошибку, препятствующую созданию приложений dotnet для использования в Linux.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az webapp create` Исправлена ошибка для сохранения существующих параметров AppSettings с помощью az webapp create.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az webapp up` Исправлена ошибка для создания группы ресурсов для команды az webapp up при использовании флага -g.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az webapp config` Исправлена ошибка для отображения значений для выходных данных не в формате JSON с помощью команды az webapp config connection-string list.

### <a name="arm"></a>ARM

* `az deployment create/validate`: добавлен параметр `--no-prompt` для пропуска запроса отсутствующих параметров для шаблона ARM.
* `az deployment group/mg/sub/tenant validate`: включена поддержка комментариев в файле параметров развертывания.
* `az deployment`: удалено значение `is_preview` для параметра `--handle-extended-json-format`.
* `az deployment group/mg/sub/tenant cancel`: включена поддержка отмены развертывания для шаблона ARM.
* `az deployment group/mg/sub/tenant validate`: улучшено отображение сообщения об ошибке при сбое проверки развертывания.
* `az deployment-scripts`: добавлена новая команда для DeploymentScripts.
* `az resource tag`: добавлен параметр `--is-incremental` для инкрементного добавления тегов к ресурсам.

### <a name="aro"></a>ARO

* `az aro`:  добавлен модуль команды aro Azure RedHat OpenShift версии 4.

### <a name="batch"></a>Пакетная служба Azure

* Обновлен API пакетной службы.

### <a name="compute"></a>Службы вычислений

* `az sig image-version create`: добавлен тип учетной записи хранения Premium_LRS.
* `az vmss update`: устранена проблема с обновлением уведомления о завершении.
* `az vm/vmss create`: включена поддержка версии специализированного образа.
* API SIG версии 2019-12-01
* `az sig image-version create`: добавлен параметр --target-region-encryption.
* Исправлена ошибка, из-за которой тесты завершались сбоем при последовательном выполнении из-за дублирования имени хранилища ключей в глобальном кэше в памяти.

### <a name="cosmosdb"></a>Cosmos DB

* Включена поддержка `az cosmosdb private-link-resource/private-endpoint-connection`.

### <a name="iot-central"></a>IoT Central

* Прекращена поддержка `az iotcentral`.
* Добавлен модуль команды `az iot central`.

### <a name="monitor"></a>Монитор

* Включена поддержка сценария приватного канала для монитора.
* Исправлен неправильный способ имитации в test_monitor_general_operations.py.

### <a name="network"></a>Сеть

* Прекращена поддержка SKU для команды public ip update.
* `az network private-endpoint`: включена поддержка закрытой группы зон DNS.
* Включена функция локального контекста для параметра vnet/subnet.
* Исправлен неправильный пример использования в test_nw_flow_log_delete.

### <a name="packaging"></a>Упаковка

* Прекращена поддержка пакета Ubuntu/Disco.

### <a name="rbac"></a>RBAC

* `az ad app create/update`: включена поддержка параметра --optional-claims.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлены команды администратора Azure Active Directory для PostgreSQL и MySQL.

### <a name="service-fabric"></a>Service Fabric

* Исправление 12891: `az sf application update --application-parameters` удаляет старые параметры, отсутствующие в запросе.
* Исправление 12470: включена поддержка az sf create cluster, исправлены ошибки, связанные с устойчивостью и надежностью обновления, поиск VMSS выполняется корректно в коде при указании имени типа узла.

### <a name="sql"></a>SQL

* Добавлены команды `az sql mi op list`, `az sql mi op get`, `az sql mi op cancel`.
* `az sql midb`: обновление и отображение политик долгосрочного хранения, отображение и удаление долгосрочных резервных копий, восстановление долгосрочных резервных копий.

### <a name="storage"></a>Память

* Обновлена версия azure-mgmt-storage до 9.0.0.
* `az storage logging off`: включено отключение ведения журналов для учетной записи хранения.
* `az storage account update`: включена автоматическая смена ключа для CMK.
* `az storage account encryption-scope create/update/list/show`: включена настройка области шифрования.
* `az storage container create`: добавлены параметры --default-encryption-scope и --deny-encryption-scope-override для настройки области шифрования на уровне контейнера.

### <a name="survey"></a>Опрос

* Добавлен параметр для отключения ссылки опроса.

## <a name="april-01-2020"></a>01 апреля 2020 г.

Версия 2.3.1

### <a name="acr"></a>ACR

* Исправлена неправильная версия azure-mgmt-containerregistry для Linux.

### <a name="profile"></a>Профиль

* az login: Исправлена ошибка входа в облачных профилях, отличающихся от `latest`.

## <a name="march-31-2020"></a>31 марта 2020 г.

Версия 2.3.0

### <a name="acr"></a>ACR

* az acr task update: исключение пустого указателя.
* `az acr import`: Отредактировано справочное сообщение и сообщение об ошибке для уточнения того, как использовать параметры --source и --registry.
* Добавлено средство проверки для аргумента registry_name.
* `az acr login`: удален флаг предпросмотра для --expose-token.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр ветви az acr task create/update.
* Клиент az acr task update теперь может независимо обновлять контекст, токен GitHub и триггеры.
* az acr agentpool: новая функция.

### <a name="aks"></a>AKS

* Исправлена ошибка с apiServerAccessProfile при обновлении --api-server-authorized-ip-ranges.
* Обновление AKS: при обновлении исходящие IP-адреса переопределяются с помощью входных значений.
* Не создаются имена субъектов-служб для кластеров MSI и реализована поддержка присоединение ACR к кластерам MSI.

### <a name="ams"></a>AMS

* Исправление 12469: не удается добавить content-key-policy для FairPlay из-за проблем с параметром ask.

### <a name="appconfig"></a>AppConfig

* Добавлен параметр --skip-keyvault для экспорта kv.

### <a name="appservice"></a>AppService

* Исправление 12509: удален тег, добавлявшийся по умолчанию при создании приложения с помощью az webapp up.
* az functionapp create: обновлено меню справки для --runtime-version и добавлено предупреждение, когда пользователь указывает параметр --runtime-version для DotNet.
* az functionapp create: изменен способ установки JavaVersion для приложений-функций Windows.

### <a name="arm"></a>ARM

* az deployment create/validate: по умолчанию используется параметр --handle-extended-json-format.
* az lock create: в справочную документацию добавлены примеры создания подресурсов.
* Список az deployment {group/mg/sub/tenant}: реализована поддержка фильтрации ProvisioningState.
* az deployment: исправлена ошибка синтаксического анализа комментария в последнем аргументе.

### <a name="backup"></a>Резервное копирование

* Добавлена возможность восстановления нескольких файлов.
* Добавлена возможность резервного копирования только дисков с ОС.
* Добавлен параметр restore-as-unmanaged-disk, позволяющий задать неуправляемое восстановление.

### <a name="compute"></a>Службы вычислений

* az vm create: для --nsg-rule добавлен вариант NONE.
* az vmss create/update: удален тег предпросмотра для автоматического восстановления VMSS.
* az vm update: реализована поддержка --workspace.
* Исправлена ошибка в коде инициализации VirtualMachineScaleSetExtension.
* Версия VMAccessAgent обновлена до 2.4.
* az vmss set-orchestration-service-state: реализована поддержка состояния службы оркестрации наборов VMSS.
* Версия API диска обновлена до 2019-11-01.
* az disk create: add --disk-iops-read-only, --disk-mbps-read-only, --max-shares, --image-reference, --image-reference-lun, --gallery-image-reference, --gallery-image-reference-lun.

### <a name="cosmos-db"></a>Cosmos DB

* Добавлен отсутствовавший параметр --type для перенаправления в связи с устареванием.

### <a name="docker"></a>Docker

* Обновление до Alpine 3.11 и Python 3.6.10.

### <a name="extension"></a>Расширение

* Добавлена возможность загрузки расширений в системный путь через пакеты.

### <a name="hdinsight"></a>HDInsight

* (az hdinsight create:) Добавлена возможность указания клиентами минимальной поддерживаемой версии TLS с помощью параметра `--minimal-tls-version`. Допустимые значения: 1.0,1.1,1.2.

### <a name="iot"></a>Интернет вещей

* Добавлен параметр codeowner.
* az iot hub create: номер SKU по умолчанию изменен с F1 на S1.
* iot hub: реализована поддержка IotHub в профиле 2019-03-01-hybrid.

### <a name="iotcentral"></a>IoT Central

* Обновлены сведения об ошибках, шаблон приложения по умолчанию и сообщение с подсказкой.

### <a name="keyvault"></a>Хранилище ключей

* Реализована поддержка резервного копирования и восстановления сертификатов.
* keyvault create/update: добавлена поддержка параметра --retention-days.
* При перечислении больше не показываются управляемые ключи и секреты.
* az keyvault create: реализована поддержка `--network-acls`, `--network-acls-ips` и `--network-acls-vnets` для указания сетевых правил при создании хранилища.

### <a name="lock"></a>Блокировка

* Исправлена ошибка с командой az lock delete, не работавшей с Microsoft.DocumentDB.

### <a name="monitor"></a>Монитор

* az monitor clone: добавлена возможность клонирования правил метрики из одного ресурса в другой.
* Исправлена ошибка IcM179210086: не удается создать настраиваемое оповещение для метрики Application Insights.

### <a name="netappfiles"></a>NetAppFiles

* az volume create: для томов защиты данных добавлены операции репликации (approve, suspend, resume, status, remove).

### <a name="network"></a>Сеть

* az network application-gateway waf-policy managed-rule rule-set add: добавлена поддержка Microsoft_BotManagerRuleSet.
* Журнал потоков наблюдателя за сетями: исправлены неправильные сведения об устаревании.
* Добавлена поддержка имен узлов в прослушивателе шлюза приложений.
* az network nat gateway: добавлена возможность создания пустого ресурса без общедоступного IP-адреса или общедоступного IP-префикса.
* Добавлена возможность создания VPN-шлюза.
* Реализована поддержка `--if-none-match` для `az network dns record-set {} add-record`.

### <a name="packaging"></a>Упаковка

* Прекращена поддержка Python 3.5.

### <a name="profile"></a>Профиль

* az login: добавлен показ предупреждений об ошибках MFA.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлены команды управления ключами шифрования данных сервера для PostgreSQL и MySQL.

## <a name="march-10-2020"></a>10 марта 2020 г.

Версия 2.2.0

### <a name="acr"></a>ACR

* Исправлена команда `az acr login`, которая неправильно вызывала ошибку.
* Добавлена новая команда `az acr helm install-cli`.
* Добавлена частная ссылка и включена поддержка CMK.
* Добавлена команда private-link-resource list.

### <a name="aks"></a>AKS

* Исправлена функция поиска AKS в Cloud Shell.
* az aks: устранены ошибки NoneType при мониторинге надстроек и пула агентов.
* Добавлен параметр --nodepool-tags в пуле узлов при создании кластера Azure Kubernetes.
* Добавлен параметр --tags при добавлении пула узлов в кластер или его обновлении.
* aks create: добавлен параметр `--enable-private-cluster`.
* Добавлен параметр --nodepool-labels в пуле узлов при создании кластера Azure Kubernetes.
* Добавлен параметр --labels при добавлении нового пула узлов в кластер Azure Kubernetes.
* Добавлен отсутствующий символ / в URL-адрес панели мониторинга.
* Включена поддержка создания кластеров AKS для управляемых удостоверений
* az aks: включена проверка состояния сетевого подключаемого модуля: Azure или Kubenet.
* az aks: включена поддержка ключа сеанса AAD.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] az aks: включена поддержка изменений MSI для GF и BF для omsagent (мониторинг контейнеров) (1)
* az aks use-dev-spaces: добавлен параметр типа конечной точки в команду use-dev-spaces для настройки конечной точки, созданной в контроллере Azure Dev Spaces.

### <a name="appconfig"></a>AppConfig

* Включена разблокировка с использованием kv set для добавления функции и ссылки на хранилище ключей.

### <a name="appservice"></a>AppService

* az webapp create: устранена проблема с выполнением команды с параметром --runtime.
* az functionapp deployment source config-zip: добавлено сообщение об ошибке, если группа ресурсов или имя функции недействительны или не существуют.
* functionapp create: исправлено сообщение с предупреждением, которое появляется с `functionapp create` и в котором указан флаг `--functions_version`, но в имени флага ошибочно используется `_` вместо `-`.
* az functionapp create: обновлен способ настройки linuxFxVersion и имени образа контейнера для приложений с функциями Linux.
* az functionapp deployment source config-zip: устранена проблема, вызванная изменением параметров приложения во время развертывания ZIP, что приводит к ошибкам 5xx во время развертывания.
* Исправление 5720946: не удается задать имя с помощью az webapp backup.

### <a name="arm"></a>ARM

* az resource: улучшены примеры для модуля ресурсов.
* az policy assignment list: Включена поддержка списков назначений политик в области группы управления.
* Добавлены команды `az deployment group` и `az deployment operation group` для развертывания шаблонов в группах ресурсов. Это дубликат `az group deployment` и `az group deployment operation`.
* Добавлены команды `az deployment sub` и `az deployment operation sub` для развертывания шаблонов в области подписки. Это дубликат `az deployment` и `az deployment operation`.
* Добавлены команды `az deployment mg` и `az deployment operation mg` для развертывания шаблонов в группах управления.
* Добавлены команды `az deployment tenant` и `az deployment operation tenant` для развертывания шаблонов в области арендатора.
* az policy assignment create: добавлено описание параметра `--location`.
* az group deployment create: добавлен параметр `--aux-tenants` для включения поддержки перекрестных арендаторов.

### <a name="cdn"></a>CDN

* Добавлены команды WAF CDN.

### <a name="compute"></a>Службы вычислений

* az sig image-version: добавлен параметр --data-snapshot-luns
* az ppg show: добавлен параметр --colocation-status, чтобы включить выборку состояния совместного размещения всех ресурсов в группе размещения близкого взаимодействия.
* az vmss create/update: включена поддержка автоматического исправления.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] az image template: шаблон переименован в построитель.
* az image builder create: добавлен параметр --image-template.

### <a name="cosmos-db"></a>Cosmos DB

* Добавлены командлеты хранимой процедуры SQL, определяемых пользователем функций и триггеров.
* az cosmosdb create: добавлен параметр --key-uri для добавления сведений о шифровании хранилища ключей.

### <a name="keyvault"></a>Хранилище ключей

* keyvault create: включена функция обратимого удаления по умолчанию.

### <a name="monitor"></a>Монитор

* az monitor metrics alert create: включена поддержка `~` в `--condition`.

### <a name="network"></a>Сеть

* az network application-gateway rewrite-rule create: включена поддержка конфигурации URL-адресов.
* az network dns zone import: для параметра --zone-name в будущем можно будет не учитывать регистр.
* az network private-endpoint/private-link-service: удалена метка предварительной версии.
* az network bastion: включена поддержка бастиона.
* az network vnet list-available-ips: включена поддержка списка доступных IP-адресов в виртуальной сети.
* az network watcher flow-log create/list/delete/update: добавлены новые команды для управления журналами потоков Наблюдателя и предоставлен параметр --location для явного определения Наблюдателя.
* az network watcher flow-log configure: поддержка прекращена.
* az network watcher flow-log show: включена поддержка параметров --location и --name для получения результатов в формате ARM; поддержка предыдущего форматированного вывода прекращена.

### <a name="policy"></a>Политика

* az policy assignment create: исправлена ошибка, из-за которой автоматически генерируемое имя назначения политики превышало предельное значение.

### <a name="rbac"></a>RBAC

* az ad group show: исправлено значение --group, обрабатываемое как проблема с регулярными выражениями.

### <a name="rdbms"></a>Реляционная СУБД

* Обновлена версия пакета SDK azure-mgmt-rdbms до 2.0.0.
* az postgres private-endpoint-connection: включено управление подключениями частных конечных точек Postgres.
* az postgres private-link-resource: включено управление ресурсами частных ссылок Postgres.
* az mysql private-endpoint-connection: включено управление подключениями частных конечных точек MySQL.
* az mysql private-link-resource: включено управление ресурсами частных ссылок MySQL.
* az mariadb private-endpoint-connection: включено управление подключениями частных конечных точек MariaDB.
* az mariadb private-link-resource: включено управление ресурсами частных ссылок MariaDB.
* Обновление тестов частной конечной точки RDBMS

### <a name="sql"></a>SQL

* Sql midb Add: list-deleted, show-deleted, update-retention, show-retention.
* (sql server create:) добавлен необязательный флаг включения и отключения доступа к общедоступным сетям в команду создания SQL Server.
* (sql server update:) внесены некоторые изменения для клиентов.
* Добавлено свойство minimal_tls_version для MI и SQL DB.

### <a name="storage"></a>Память

* az storage blob delete-batch: исправлено неправильное поведение флага `--dryrun`.
* az storage account network-rule add (исправление): добавлено требование того, чтобы операция была идемпотентной.
* az storage account create/update: включена поддержка предпочтения маршрутизации.
* Обновлена версия azure-mgmt-storage до 8.0.0.
* az storage container immutability create: добавлен параметр --allow-protected-append-write.
* az storage account private-link-resource list: включена поддержка вывода списка ресурсов частной ссылки для учетной записи хранения.
* az storage account private-endpoint-connection approve/reject/show/delete: включена поддержка управления подключениями к частным конечным точкам.
* az storage account blob-service-properties update: добавлены параметры --enable-restore-policy и --restore-days.
* az storage blob restore: включена поддержка восстановления диапазонов BLOB-объектов.

## <a name="february-18-2020"></a>18 февраля 2020 г.

Версия 2.1.0

### <a name="acr"></a>ACR

* Добавлен новый аргумент `--expose-token` для `az acr login`.
* Исправлены неправильные выходные данные `az acr task identity show -n Name -r Registry -o table`.
* az acr login: отображение CLIError при наличии ошибок, возвращаемых командой docker.

### <a name="acs"></a>ACS

* aks create/update: добавление проверки `--vnet-subnet-id`.

### <a name="aladdin"></a>Aladdin

* Выполнение синтаксического анализа созданных примеров в _help.py для команд.

### <a name="ams"></a>AMS

* az ams теперь предоставляется в общедоступной версии

### <a name="appconfig"></a>AppConfig

* Исправлено справочное сообщение, чтобы исключить неподдерживаемый фильтр ключей или меток.
* Удален тег preview для большинства команд, кроме управляемого удостоверения и флагов функций.
* Добавлен управляемый ключ клиента при обновлении магазинов.

### <a name="appservice"></a>AppService

* az webapp list-runtimes: исправлена ошибка для list-runtimes.
* Добавлена команда az webapp|functionapp config ssl create.
* Включена поддержка приложений-функций версии 3 и Node 12.

### <a name="arm"></a>ARM

* az policy assignment create: исправлено сообщение об ошибке, если параметр `--policy` является недопустимым.
* az group deployment create: Устранена ошибка stat: path too long for Windows при использовании большого файла parameters.json.

### <a name="backup"></a>Резервное копирование

* Исправлен поток восстановления на уровне элемента в OLR.
* Включена поддержка восстановления в виде файлов для баз данных SQL и SAP.

### <a name="compute"></a>Службы вычислений

* vm/vmss/availability-set update: add --ppg: разрешено обновление ProximityPlacementGroup.
* vmss create: add --data-disk-iops and --data-disk-mbps
* az vm host: удален тег preview для `vm host` и `vm host group`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Исправление 10728. `az vm create`: автоматическое создание подсети, если указанные виртуальная сеть и подсеть не существуют.
* Повышена надежность списка образов виртуальных машин.

### <a name="eventhub"></a>Eventhub

* Включена поддержка Azure Stack для профиля 2019-03-01-hybrid.

### <a name="keyvault"></a>Хранилище ключей

* az keyvault key create: добавлено новое значение `import` для параметра `--ops`.
* az keyvault key list-versions: включена поддержка параметров `--id` для определения ключей.
* Включена поддержка подключений к частным конечным точкам.

### <a name="network"></a>Сеть

* Выполнена активация azure-mgmt-network 9.0.0.
* az network private-link-service update/create: включена поддержка --enable-proxy-protocol.
* Добавлена функция монитора подключения версии 2.

### <a name="packaging"></a>Упаковка

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Прекращена поддержка Python 2.7.

### <a name="profile"></a>Профиль

* Предварительный просмотр: В учетные записи подписок добавлены новые атрибуты `homeTenantId` и `managedByTenants`. Чтобы изменения вступили в силу, повторно выполните команду `az login`.
* az login: отображение предупреждения, если подписка связана с несколькими клиентами, но по умолчанию используется с первым из них (чтобы выбрать определенный клиент при доступе к этой подписке, включите `--tenant` в `az login`).

### <a name="role"></a>Роль

* az role assignment create: исправлена ошибка с кодом HTTP 400, возникающая при присвоении роли субъекту-службе по отображаемому имени.

### <a name="sql"></a>SQL

* Обновлен командлет `az sql mi update` Управляемого экземпляра SQL (добавлены два новых параметра: tier и family).

### <a name="storage"></a>Память

* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az storage account create`: Тип учетной записи хранения по умолчанию изменен на StorageV2.

## <a name="february-04-2020"></a>4 февраля 2020 г.

Версия 2.0.81

### <a name="acs"></a>ACS

* Добавлена возможность задания выделенных исходящих портов и времени ожидания подсистемы балансировки нагрузки уровня "Стандартный".
* Выполнено обновление до API версии 2019-11-01.

### <a name="acr"></a>ACR

* [Критическое изменение] `az acr delete` будет выводить запрос.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда az acr task delete будет выводить запрос.
* Добавлена новая группа команд az acr taskrun show/list/delete для управления выполнением задач.

### <a name="aks"></a>AKS

* Каждый кластер получает отдельный субъект-службу для улучшения изоляции.

### <a name="appconfig"></a>AppConfig

* Поддержка импорта ссылок на хранилище ключей из службы приложений и их экспорта в нее.
* Поддержка импорта и экспорта всех меток между файлами appconfig.
* Проверка имен ключей и функций перед настройкой и импортом.
* Предоставление изменений номера SKU в хранилище конфигураций.
* Новая группа команд для управляемого удостоверения.

### <a name="appservice"></a>AppService

* Azure Stack: команды поверхности в профиле 2019-03-01-hybrid
* functionapp: добавлена возможность создавать приложения-функции Java в Linux.

### <a name="arm"></a>ARM

* Исправление ошибки 10246: аварийное завершение `az resource tag` в случае, если переданный параметр `--ids` являлся идентификатором группы ресурсов.
* Исправление ошибки 11658: команда `az group export` не поддерживала параметры `--query` и `--output`.
* Исправление ошибки 10279: код выхода `az group deployment validate` был равен 0, если проверка не пройдена.
* Исправление ошибки 9916: улучшено сообщение об ошибке из-за конфликта между тегом и другими условиями фильтра для команды `az resource list`.
* Добавлен новый параметр `--managed-by` для добавления данных managedBy для команды `az group create`.

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

* Добавлена подгруппа `monitor` для управления мониторингом Log Analytics в кластере Azure Red Hat OpensShift.

### <a name="botservice"></a>Служба Bot

* Исправление ошибки 11697: команда `az bot create` не являлась идемпотентной.
* Проверки исправления имен изменены для выполнения только в режиме реального времени.

### <a name="cdn"></a>CDN

* Добавлена поддержка функции rulesEngine.
* Добавлена новая группа команд cdn endpoint rule для управления правилами.
* Пакет azure-mgmt-cdn обновлен до версии 4.0.0 для использования API версии 2019-04-15.

### <a name="deployment-manager"></a>Диспетчер развертывания

* Добавлена операция вывода списка всех ресурсов.
* Улучшен ресурс шага для нового типа шага.
* Пакет azure-mgmt-deploymentmanager обновлен для использования версии 0.2.0.

### <a name="iot"></a>Интернет вещей

* Команды IoT hub Job объявлены нерекомендуемыми.

### <a name="iot-central"></a>IoT Central

* Добавлена поддержка создания и обновления приложений с новыми SKU: ST0, ST1, ST2.

### <a name="key-vault"></a>Key Vault

* Добавлена новая команда `az keyvault key download` для скачивания ключей.

### <a name="misc"></a>Разное

* Исправление ошибки 6371: поддержка завершения имен файлов и переменных среды в Bash.

### <a name="network"></a>Сеть

* Исправление ошибки 2092: для команды az network dns record-set add/remove добавлено предупреждение, отображаемое, если набор записей не найден. В будущем для подтверждения этого автоматического создания будет добавлен дополнительный аргумент.

### <a name="policy"></a>Политика

* Добавлена новая команда `az policy metadata` для получения ресурсов метаданных расширенной политики.
* `az policy remediation create`: С помощью параметра `--resource-discovery-mode` можно указать, следует ли повторно оценить соответствие перед исправлением.

### <a name="profile"></a>Профиль

* `az account get-access-token`: Добавлен параметр `--tenant` для получения маркера для арендатора напрямую, без необходимости указывать подписку.

### <a name="rbac"></a>RBAC

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Исправление ошибки 11883: `az role assignment create`: пустая область приведет к ошибке.

### <a name="security"></a>Безопасность

* Добавлены новые команды `az atp show` и `az atp update` для просмотра и настройки дополнительных параметров защиты от угроз для учетных записей хранения.

### <a name="sql"></a>SQL

* `sql dw create`: параметры `--zone-redundant` и `--read-replica-count` объявлены нерекомендуемыми. Эти параметры не применяются к хранилищу данных.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az sql db create`: Значения WideWorldImportersStd и WideWorldImportersFull больше не являются задокументированным допустимыми значениями для команды az sql db create --sample-name. Эти примеры базы данных всегда будут приводить к сбою создания.
* Добавлены новые команды `sql db classification show/list/update/delete` и `sql db classification recommendation list/enable/disable` для управления классификациями конфиденциальности баз данных SQL.
* `az sql db audit-policy`: устранены пустые действия аудита и группы.

### <a name="storage"></a>Память

* Добавлена новая группа команд `az storage share-rm` для использования поставщика ресурсов Microsoft.Storage в операциях управления файловыми ресурсами Azure.
* Исправление ошибки 11415: ошибка разрешения для `az storage blob update`.
* Добавлены интеграция AzCopy 10.3.3 и поддержка Win32.
* `az storage copy`: добавлены параметры `--include-path`, `--include-pattern`, `--exclude-path` и `--exclude-pattern`.
* `az storage remove`: параметры `--inlcude` и `--exclude` заменены параметрами `--include-path`, `--include-pattern`, `--exclude-path` и `--exclude-pattern`.
* `az storage sync`: добавлены параметры `--include-pattern`, `--exclude-path` и `--exclude-pattern`.

### <a name="servicefabric"></a>Service Fabric

* Добавлены новые команды для управления приложениями и службами.

## <a name="january-13-2020"></a>13 января 2020 г.

Версия 2.0.80

### <a name="compute"></a>Службы вычислений

* Обновление диска: добавлены параметры --disk-encryption-set и --encryption-type.
* Создание и обновление моментального снимка: добавлены параметры --disk-encryption-set и --encryption-type.

### <a name="storage"></a>Память

* Обновление azure-mgmt-storage до версии 7.1.0
* `az storage account create`: добавлены параметры `--encryption-key-type-for-table` и `--encryption-key-type-for-queue` для включения поддержки службы шифрования таблиц и очередей.

## <a name="january-07-2020"></a>7 января 2020 г.

Версия 2.0.79

### <a name="acr"></a>ACR

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр --os для команд acr build, acr task create/update, acr run и acr pack. Вместо этого используется параметр --platform.

### <a name="appconfig"></a>AppConfig

* Добавлена поддержка импорта и экспорта флагов функций.
* Добавлена новая команда az appconfig kv set-keyvault для создания ссылки на хранилище ключей.
* Добавлена поддержка различных соглашений об именовании при экспорте флагов функций в файл.

### <a name="appservice"></a>AppService

* Устранена проблема № 7154: обновлена документация по команде <> — теперь в ней используются обратные, а не одинарные кавычки.
* Устранена проблема № 11287 (webapp up): по умолчанию для приложения, созданного с использованием этого флага, должен быть включен SSL.
* Устранена проблема № 11592: добавлен флаг az webapp up для статических сайтов HTML.

### <a name="arm"></a>ARM

* Исправлена ошибка с командой `az resource tag`: невозможно было обновить теги хранилища Служб восстановления.

### <a name="backup"></a>Резервное копирование

* Добавлена новая команда backup protection undelete для включения функции обратимого удаления рабочей нагрузки IaasVM.
* Добавлен новый параметр --soft-delete-feature-state для команды set backup-properties.
* Добавлена поддержка исключения диска для рабочей нагрузки IaasVM.

### <a name="compute"></a>Службы вычислений

* Исправлен сбой `vm create` в профиле Azure Stack.
* Добавлена поддержка определений списков и метрик запросов для виртуальной машины (vm monitor metrics tail/list-definitions).
* Добавлено новое действия повторного применения команды az vm

### <a name="hdinsight"></a>HDInsight

* Включена поддержка создания кластера Kafka с помощью прокси-сервера Kafka RESTful.
* Обновление azure-mgmt-hdinsight до версии 1.3.0

### <a name="misc"></a>Прочее

* Добавлена команда `az version show` предварительного просмотра для отображения версий модулей и расширений Azure CLI в формате JSON по умолчанию или в формате, настроенном с помощью параметра --output

### <a name="event-hubs"></a>Центры событий

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр состояния ReceiveDisabled из команд az eventhubs eventhub update и az eventhubs eventhub create. Данный параметр недопустим для сущностей концентратора событий.

### <a name="service-bus"></a>Служебная шина

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр состояния ReceiveDisabled из команд az servicebus topic create, az servicebus topic update, az servicebus queue create и az servicebus queue update. Этот параметр является недопустимым для разделов и очередей служебной шины.

### <a name="rbac"></a>RBAC

* Устранена проблема № 11712: команда `az ad app/sp show` не возвращала код выхода 3, если приложение или субъект-служба не существует.

### <a name="storage"></a>Память

* `az storage account create`: удален флаг предварительного просмотра для параметра --enable-hierarchical-namespace.
* Хранилище azure-mgmt-storage обновлено до версии 7.0.0 для использования API версии 2019-06-01.
* Добавлены новые параметры (`--enable-delete-retention` и `--delete-retention-days`) для поддержки управления политикой хранения удаленных свойств службы BLOB-объектов учетной записи хранения.

## <a name="december-17-2019"></a>17 декабря 2019 г.

2.0.78

### <a name="acr"></a>ACR

* Добавлена поддержка локального контекста во время выполнения задачи ACR.

### <a name="acs"></a>ACS

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В команде az openshift create параметр `--workspace-resource-id` переименован на `--workspace-id`.

### <a name="ams"></a>AMS

* Команды show обновлены для возвращения ответа 3, если ресурс не найден.

### <a name="appconfig"></a>AppConfig

* Исправлена ошибка, возникающая при добавлении api-version в URL-адрес запроса. Имеющееся решение не работает с разбивкой на страницы.
* Добавлена поддержка отображения языков, кроме английского, так как наша внутренняя служба поддерживает Юникод для глобализации.

### <a name="appservice"></a>AppService

* Устранена проблема № 11217 (webapp): теперь команда az webapp config ssl upload поддерживает параметр слота.
* Устранена проблема № 10965. Ошибка: Имя не может быть пустым. Разрешено удаление по IP-адресу и подсети.
* Добавлена поддержка импорта сертификатов из хранилища ключей: `az webapp config ssl import`

### <a name="arm"></a>ARM

* Обновлен пакет ресурсов azure-mgmt-resource для использования версии 6.0.0
* Добавлена межклиентская поддержка команды `az group deployment create` путем добавления нового параметра `--aux-subs`
* Добавлен новый параметр `--metadata` для поддержки добавления метаданных определений наборов политик.

### <a name="backup"></a>Резервное копирование

* Добавлена поддержка резервного копирования для рабочей нагрузки SQL и SAP HANA.

### <a name="botservice"></a>Служба Bot

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален флаг --version из предварительной версии команды az bot create. Поддерживаются только боты пакетов SDK версии 4.
* Добавлена проверка доступности имени для команды az bot create.
* Добавлена поддержка обновления URL-адреса значка для бота с помощью команды az bot update.
* Добавлена поддержка обновления канала Direct Line с помощью команды az bot directline update.
* Добавлена поддержка флага --enable-enhanced-auth в команде az bot directline create.
* Следующие группы команд предоставляются в общедоступной, а не в предварительной версии: az bot authsetting.
* Следующие команды в az bot предоставляются в общедоступной, а не в предварительной версии: create, prepare-deploy, show, delete и update.
* Устранена проблема с командой az bot prepare-deploy, которая изменяла значение параметра --proj-file-path на нижний регистр (например, с Test.csproj на test.csproj).

### <a name="compute"></a>Службы вычислений

* vmss create/update: добавлен параметр --scale-on-policy, который определяет, какие виртуальные машины выбираются для удаления при масштабировании VMSS.
* vm/vmss update: добавлен параметр --priority.
* vm/vmss update: добавлен параметр --max-price.
* Добавлена группа команд для шифрования дисков (create, show, update, delete, list).
* disk create: добавлены параметры --encryption-type и --disk-encryption-set.
* vm/vmss create. Добавлены параметры --os-disk-encryption-set и --data-disk-encryption-sets.

### <a name="core"></a>Основные сведения

* Удалена поддержка Python версии 3.4.
* Подключение к опросу HaTS в нескольких командах.

### <a name="dls"></a>DLS

* Обновлена версия пакета SDK для ADLS (0.0.48).

### <a name="install"></a>Установка

* Добавлена поддержка установки скриптов Python 3.8.

### <a name="iot"></a>Интернет вещей

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр --failover-region из команды manual-failover. Теперь она будет выполнять отработку отказа в назначенный геопарный дополнительный регион.

### <a name="key-vault"></a>Key Vault

* Устранена проблема № 8095: (`az keyvault storage remove`): улучшено справочное сообщение.
* Устранена проблема № 8921 (`az keyvault key/secret/certificate list/list-deleted/list-versions`): исправлена ошибка проверки в параметре `--maxresults`.
* Устранена проблема № 10512 (`az keyvault set-policy`): улучшено сообщение об ошибке, возвращаемое, если не указан ни один из параметров `--object-id`, `--spn` или `--upn`.
* Устранена проблема № 10846 (`az keyvault secret show-deleted`): при указании значения `--id` значение `--name/-n` не требовалось.
* Устранена проблема № 11084: (`az keyvault secret download`): улучшено справочное сообщение параметра `--encoding`.

### <a name="network"></a>Сеть

* az network application-gateway probe: добавлена поддержка параметра --port, позволяющего указать порт, который используется для проверки внутренних серверов при создании и обновлении.
* az network application-gateway url-path-map create/update: исправлена ошибка с `--waf-policy`.
* az network application-gateway: добавлена поддержка параметра `--rewrite-rule-set`.
* az network list-service-aliases: добавлена поддержка перечисления псевдонимов служб, которые можно использовать для политик конечной точки службы.
* az network dns zone import: добавлена поддержка символа .@ в имени записи.

### <a name="packaging"></a>Упаковка

* Снова добавлены сборки Edge для установки PIP.
* Добавлен пакет Ubuntu eoan.

### <a name="policy"></a>Политика

* Добавлена поддержка API Политики версии 2019-09-01.
* az policy set-definition: добавлена поддержка группирования в определениях наборов политик с помощью параметра `--definition-groups`.

### <a name="redis"></a>Redis

* В команду `az redis create` добавлена предварительная версия параметра `--replicas-per-master`.
* Обновлена версия azure-mgmt-redis с 6.0.0 на 7.0.0 RC1.

### <a name="servicefabric"></a>Service Fabric

* Исправлена логика добавления типа узла, а также устранена проблема № 10963: при добавлении нового типа узла с уровнем устойчивости Gold возникала ошибка CLI.
* Обновлена версия параметра ServiceFabricNodeVmExt до 1.1 в шаблоне создания.

### <a name="sql"></a>SQL

* В команды create и update базы данных SQL добавлены параметры --read-scale и --read-replicas для поддержки управления масштабированием операций чтения.

### <a name="storage"></a>Память

* Выпущена общедоступная версия больших файловых ресурсов для команды создания и обновления учетной записи хранения.
* Выпущена общедоступная версия поддержки маркера SAS для делегирования пользователя.
* Добавлены новые команды (`az storage account blob-service-properties show` и `az storage account blob-service-properties update --enable-change-feed`) для управления свойствами службы BLOB-объектов учетной записи хранения.
* [ОЖИДАЕТСЯ КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ] `az storage copy`: символ `*` больше не поддерживается в качестве подстановочного знака в URL-адресе. Тем не менее новые параметры--include-pattern и--exclude-pattern будут добавлены с поддержкой подстановочных знаков `*`.
* Устранена проблема № 11043: добавлена поддержка удаления всего контейнера или общего ресурса в команде `az storage remove`.

## <a name="november-26-2019"></a>26 ноября 2019 г.

Версия 2.0.77

### <a name="acr"></a>ACR

* Параметр `--branch`, используемый при обновлении или создании задачи ACR, объявлен устаревшим.

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

* Добавлен флаг `--workspace-resource-id` для создания кластера Azure Red Hat Openshift с мониторингом.
* Добавлен флаг `monitor_profile` для создания кластера Azure Red Hat OpenShift с мониторингом.

### <a name="aks"></a>AKS

* Включена поддержка операции смены сертификата кластера с использованием команды az aks rotate-certs.

### <a name="appconfig"></a>AppConfig

* Включена поддержка использования символа ":" для разделителя `as az appconfig kv import`.
* Исправлена проблема с перечислением значений ключей с несколькими метками, включая метку NULL. 
* Обновлен пакет SDK для плоскости управления, azure-mgmt-appconfiguration, до версии 0.3.0. 

### <a name="appservice"></a>AppService

* Исправлена ошибка № 11100. Использование AttributeError для az webapp up при создании плана службы.
* az webapp up. При принудительном создании или развертывании сайта для поддерживаемых языков значения по умолчанию не используются.
* Включена поддержка Среды службы приложений: az appservice ase show | list | list-addresses | list-plans | create | update | delete.

### <a name="backup"></a>Резервное копирование

* Исправлена проблема в команде az backup policy list-associated-items. Добавлен необязательный параметр BackupManagementType.

### <a name="compute"></a>Службы вычислений

* Обновлена версия API вычислений, дисков, моментальных снимков до 2019-07-01.
* vmss create. Улучшение для --orchestration-mode.
* sig image-definition create. Добавлен параметр --os-state для указания того, являются ли виртуальные машины, созданные в этом образе, универсальными или специализированными.
* sig image-definition create. Добавлен параметр --hyper-v-generation для указания создания гипервизора.
* sig image-version create. Включена поддержка параметров --os-snapshot и --data-snapshots.
* image create. Включена поддержка параметра --data-disk-caching для указания параметра кэширования для дисков данных.
* Обновлена версия пакета SDK для вычислений Python до 10.0.0.
* vm/vmss create. Добавлен фрагмент Spot в свойство перечисления Priority.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименование параметра --max-billing в --max-price для виртуальных машин и Масштабируемых наборов виртуальных машин в соответствии с командлетами Swagger и PowerShell.
* vm monitor log show. Включена поддержка запроса журналов в связанной рабочей области Log Analytics.

### <a name="iot"></a>Интернет вещей

* Исправление № 2531. Добавлены вспомогательные аргументы для обновления концентратора.
* Исправление № 8323. Добавлены недостающие параметры для создания пользовательской конечной точки хранилища.
* Исправлена ошибка регрессии. Отменены изменения, переопределяющие конечную точку хранилища по умолчанию.

### <a name="key-vault"></a>Key Vault

* Исправление № 11121. При использовании `az keyvault certificate list` для передачи `--include-pending` теперь не требуется значение `true` или `false`.

### <a name="netappfiles"></a>NetAppFiles

* Обновлена версия azure-mgmt-netapp до 0.7.0, которая включает некоторые дополнительные свойства тома, связанные с предстоящими операциями репликации.

### <a name="network"></a>Сеть

* application-gateway waf-config. Не рекомендуется использовать.
* application-gateway waf-policy. Добавлены управляемые правила подгрупп для контроля управляемых наборов правил и правил исключения.
* application-gateway waf-policy. Добавлен параметр политики подгруппы для управления глобальной конфигурацией политики WAF.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] application-gateway waf-policy. Переименовано правило подгруппы в пользовательское правило.
* application-gateway http-listener. Добавлен параметр --firewall-policy при создании.
* application-gateway url-path-map rule. Добавлен параметр --firewall-policy при создании.

### <a name="packaging"></a>Упаковка

* Удалена команда az wrapper в Python.
* Включена поддержка Python 3.8.
* Изменена версия на Python 3 для пакета RPM.

### <a name="profile"></a>Профиль

* Исправлена ошибка при выполнении `az login -u {} -p {}` с учетной записью Майкрософт.
* Исправлена ошибка с `SSLError` при выполнении `az login` за прокси-сервером с самозаверяющим корневым сертификатом.
* Исправлена ошибка № 10578. `az login` зависает при одновременном запуске нескольких экземпляров в Windows или WSL.
* Исправлена ошибка № 11059. Сбой выполнения `az login --allow-no-subscriptions`, если в клиенте есть подписки.
* Исправлена ошибка № 11238. После переименования подписки вход с помощью MSI приведет к тому, что одна и та же подписка появится дважды.

### <a name="rbac"></a>RBAC

* Исправлена ошибка № 10996. Исправлена ошибка с `--force-change-password-next-login` в `az ad user update`, если `--password` не указывается.

### <a name="redis"></a>Redis

* Исправлена ошибка № 2902. Предотвращена настройка конфигураций памяти при обновлении кэша с номером SKU "Базовый".

### <a name="reservations"></a>Резервирование

* Обновлена версия пакета SDK до 0.6.0
* Добавлены сведения о плане выставления счетов после вызова Get-Catalogs.
* Добавлена новая команда `az reservations reservation-order calculate` для вычисления стоимости резервирования.
* Добавлена новая команда `az reservations reservation-order purchase` для приобретения нового резервирования.

### <a name="rest"></a>Rest
* Изменена версия `az rest` на общедоступную.

### <a name="sql"></a>SQL

* Обновлена версия azure-mgmt-sql до 0.15.0.

### <a name="storage"></a>Память

* storage account create. Добавлен параметр --enable-hierarchical-namespace для включения поддержки семантики файловой системы в службе больших двоичных объектов.
* Удалено несвязанное исключение из сообщения об ошибке.
* Исправлены проблемы, из-за которых появлялось неверное сообщение об отсутствии нужных разрешений на выполнение требуемой операции при блокировке правилами сети (AuthenticationFailed).

## <a name="november-4-2019"></a>4 ноября 2019 г.

Версия 2.0.76

### <a name="acr"></a>ACR

* В команду `az acr pack build` добавлен параметр предварительной версии `--pack-image-tag`.
* Добавлена поддержка включения аудита при создании реестра.
* Включена поддержка функции RBAC, распространяющаяся на репозиторий.

### <a name="aks"></a>AKS

* В команду `az aks create` добавлены `--enable-cluster-autoscaler`, `--min-count` и `--max-count`, что позволяет автоматически масштабировать кластер для пула узлов.
* Добавлены указанные выше флаги, а также `--update-cluster-autoscaler` и `--disable-cluster-autoscaler` в команду `az aks update`, что позволяет обновлять средство автоматического масштабирования кластера.

### <a name="appconfig"></a>AppConfig

* Добавлена группа команд функции appconfig для управления флагами функций, хранимыми в Конфигурации приложений.
* Исправлена незначительная ошибка команды экспорта в файл appconfig kv. Прерывание чтения содержимого конечного файла во время экспорта.

### <a name="appservice"></a>AppService

* `az appservice plan create`: Добавлена поддержка определения persitescaling при создании плана appservice.
* Исправлена проблема, из-за которой операция webapp config ssl bind удаляла существующие теги из ресурса.
* Добавлен флаг `--build-remote` для `az functionapp deployment source config-zip` для включения поддержки действия удаленной сборки во время развертывания приложения-функции.
* Изменена версия узла по умолчанию для приложений-функций на ~10 для Windows
* В `az functionapp create` добавлено свойство `--runtime-version`.

### <a name="arm"></a>ARM

* `az deployment/group deployment validate`: В `--handle-extended-json-format` добавлен параметр для включения поддержки многострочности и комментариев в шаблоне JSON при развертывании.
* Версия azure-mgmt-resource обновлена до 2019-07-01.

### <a name="backup"></a>Резервное копирование

* Добавлена поддержка резервного копирования AzureFiles.

### <a name="compute"></a>Службы вычислений

* `az vm create`: Добавлено предупреждение при одновременном определении ускоренной сети и существующей сетевой карты.
* `az vm create`: Добавлен параметр `--vmss` для определения существующего масштабируемого набора виртуальных машин, которому должна быть назначена виртуальная машина.
* `az vm/vmss create`: Добавлена локальная копия файла псевдонима изображения, к которой можно получить доступ в ограниченной сетевой среде.
* `az vmss create`: Добавлен параметр `--orchestration-mode` для определения того, как виртуальные машины управляются масштабируемым набором.
* `az vm/vmss update`: Добавлен параметр `--ultra-ssd-enabled`, чтобы разрешить обновление параметра Ultra SSD.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az vm extension set`: Исправлена ошибка, из-за которой пользователям не удавалось определять расширение на виртуальной машине с использованием `--ids`.
* Добавлены новые команды `az vm image terms accept/cancel/show` для управления условиями использования образов Azure Marketplace.
* Расширение VMAccessForLinux обновлено до версии 1.5.

### <a name="cosmosdb"></a>Cosmos DB

* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az sql container create`: `--partition-key-path` изменен на обязательный параметр.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az gremlin graph create`: `--partition-key-path` изменен на обязательный параметр.
* `az sql container create`: Добавлены команды `--unique-key-policy` и `--conflict-resolution-policy`.
* `az sql container create/update`: Обновлена схема `--idx` по умолчанию.
* `gremlin graph create`: Добавлена команда `--conflict-resolution-policy`.
* `gremlin graph create/update`: Обновлена схема `--idx` по умолчанию.
* Исправлена опечатка в справочном сообщении.
* База данных: добавлены сведения об устаревании.
* Коллекция: добавлены сведения об устаревании.

### <a name="iot"></a>Интернет вещей

* Добавлен новый тип источника маршрутизации: DigitalTwinChangeEvents.
* Добавлены отсутствующие компоненты в `az iot hub create`.

### <a name="key-vault"></a>Key Vault

* Исправлена непредвиденная ошибка с отсутствием файла сертификата.
* Исправлена ошибка с неработающей командой `az keyvault recover/purge`.

### <a name="netappfiles"></a>NetAppFiles

* Пакет azure-mgmt-netapp обновлен до версии 0.6.0 для включения поддержки API версии 2019-07-01. Эта новая версия API включает:

    - При создании тома с использованием `--protocol-types` допускается NFSv4.1 вместо NFSv4.
    - Свойство политики экспорта томов теперь называется nfsv41, а не nfsv4.
    - Параметр `--creation-token` для тома переименован в `--file-path`.
    - Дата создания моментального снимка теперь имеет значение Created.

### <a name="network"></a>Сеть

* `az network private-dns link vnet create/update`: Добавлена поддержка связывания виртуальных сетей между клиентами.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ.] `az network vnet subnet list`: Параметры `--resource-group` и `--vnet-name` теперь являются обязательными.
* `az network public-ip prefix create`: Включена поддержка определения версии IP-адреса (IPv4, IPv6) при создании.
* Версия azure-mgmt-network обновлена до 7.0.0 и версия api-version до 2019-09-01.
* `az network vrouter`: Включена поддержка нового виртуального маршрутизатора службы и пиринга виртуальных маршрутизаторов.
* `az network express-route gateway connection`: Добавлена поддержка параметра `--internet-security`.

### <a name="profile"></a>Профиль

* Исправлена ошибка с неработающей командой `az account get-access-token --resource-type ms-graph`.
* Удалено предупреждение из `az login`.

### <a name="rbac"></a>RBAC

* Исправление `az ad app update --id {} --display-name {}` не работает.

### <a name="servicefabric"></a>Service Fabric

* `az sf cluster create`: Исправлена проблема путем изменения VMSS для вычислений с использованием файла template.json для Service Fabric Linux и Windows со стандартных дисков на управляемые.

### <a name="sql"></a>SQL

* Добавлены параметры `--compute-model`, `--auto-pause-delay` и `--min-capacity` для включения поддержки операций CRUD для нового предложения Базы данных SQL: Модель бессерверных вычислений.

### <a name="storage"></a>Память

* `az storage account create/update`: Добавлен параметр --enable-files-adds и группа аргументов свойств Azure Active Directory для включения поддержки аутентификации доменных служб Azure Active Directory для Файлов Azure.
* Расширена команда `az storage account keys list/renew` для включения поддержки перечисления или повторного создания ключей Kerberos для учетной записи хранения.

## <a name="october-15-2019"></a>15 октября 2019 г.

Версия 2.0.75

### <a name="aks"></a>AKS

* Для параметра `--load-balancer-sku` изменено значение по умолчанию на `standard`, если поддерживается версией AKS.
* Для параметра `--vm-set-type` изменено значение по умолчанию на `virtualmachinescalesets`, если поддерживается версией AKS.

### <a name="ams"></a>AMS

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Имя `job start` изменено на `job create`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В параметре `--ask` команды `content-key-policy create` вместо кодировки UTF8 теперь используется шестнадцатеричная строка из 32 символов.

### <a name="appservice"></a>AppService

* Добавлены команды `webapp config access-restriction show|set|add|remove`.
* Улучшена обработка ошибок в `webapp up`.
* Для `appservice plan update` добавлена поддержка номера SKU `Isolated`.

### <a name="arm"></a>ARM

* В `deployment create` добавлен параметр `--handle-extended-json-format` для поддержки многострочности и комментариев в шаблоне JSON.

### <a name="compute"></a>Службы вычислений

* Добавлен параметр `--enable-agent` для команды `vm create`.
* Внесены изменения в `vm create`, позволяющие автоматически использовать номер SKU "Стандартный" для общедоступных IP-адресов при использовании зон.
* Внесены изменения в `vm create`, позволяющие автоматически создавать допустимое имя для виртуальной машины, если оно не задано.
* В `vmss create` добавлен параметр `--computer-name-prefix` для поддержки пользовательского префикса имени для виртуальных машин в VMSS.
* В `vm create` добавлен параметр `--workspace` для автоматического включения рабочей области Log Analytics.
* API коллекций обновлен до версии 2019-07-01.

### <a name="core"></a>Основные сведения

* Добавлена проверка синтаксиса для параметра `--set` в универсальной команде обновления.

### <a name="iot"></a>Интернет вещей

* Исправлена проблема, при которой `iot hub show` неправильно выдавал ошибку "Ресурс не найден".

### <a name="monitor"></a>Монитор

* В `monitor log-analytics workspace` добавлена поддержка CRUD.

### <a name="network"></a>Сеть

* В `network private-dns link vnet [create|update]` добавлена поддержка виртуального канала для клиентов.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Для `network vnet subnet list` теперь требуются параметры `--resource-group` и `--vnet-name`.

### <a name="sql"></a>SQL

* В `sql mi ad-admin` добавлены команды, которые поддерживают назначение администратора AAD в управляемых экземплярах.

### <a name="storage"></a>Память

* В `storage copy` добавлен параметр `--preserve-s2s-access-tier`, позволяющий сохранить уровень доступа во время копирования между службами.
* В `storage account [create|update]` добавлен параметр `--enable-large-file-share` для поддержки общих папок большого размера в учетной записи хранения.

## <a name="september-24-2019"></a>24 сентября 2019 г.

Версия 2.0.74

### <a name="acr"></a>ACR

* В `acr config retention update` добавлен обязательный параметр `--type`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименованный параметр `--name -n` изменен на `--registry -r ` для группы команд `acr config`.

### <a name="aks"></a>AKS

* В команду `aks create` добавлен параметр `--load-balancer-sku`, позволяющий создать кластер AKS с SLB.
* В команды `aks [create|update]` добавлены параметры `--load-balancer-managed-outbound-ip-count`, `--load-balancer-outbound-ips` и `--load-balancer-outbound-ip-prefixes`, позволяющие обновлять профиль подсистемы балансировки нагрузки у кластера AKS с SLB.
* В команду `aks create` добавлен параметр `--vm-set-type`, позволяющий указывать типы виртуальных машин в кластере AKS.

### <a name="arm"></a>ARM

* В команду `group deployment create` добавлен параметр `--handle-extended-json-format`, для поддержки многострочности и комментариев в шаблоне JSON.

### <a name="compute"></a>Службы вычислений

* В команды `vmss [create|update]` добавлен параметр `--terminate-notification-time`, поддерживающий завершение настройки запланированных событий.
* В команду `vmss update` добавлен параметр `--enable-terminate-notification`, поддерживающий завершение настройки запланированных событий.
* В команды `[vm|vmss] create` добавлены параметры `--priority,`, `--eviction-policy,` и `--max-billing`.
* Изменена команда `disk create`, которая теперь позволяет указать точный размер отправки на диск.
* В `snapshot create` добавлена поддержка добавочных моментальных снимков для управляемых дисков.

### <a name="cosmos-db"></a>Cosmos DB

* В команду `cosmosdb keys list` добавлен параметр `--type <key-type>` для отображения ключей, ключей только для чтения или строк подключения.
* Добавлена команда `cosmosdb keys regenerate`.
* [УСТАРЕЛО] Команды `cosmosdb list-connection-strings`, `cosmosdb regenerate-key` и `cosmosdb list-read-only-keys` больше не поддерживаются.

### <a name="eventgrid"></a>Сетка событий

* Исправлен текст справки по конечной точке — дана ссылка на правильный параметр.

### <a name="key-vault"></a>Key Vault

* Исправлена проблема, из-за которой вход с помощью клиента (`login -t`) мог приводить к сбою `keyvault create`.

### <a name="monitor"></a>Монитор

* Исправлена проблема с тем, что символ `:` являлся недопустимым в аргументе `--condition` для `monitor metrics alert create`.

### <a name="policy"></a>Политика

* Добавлена поддержка API Политики версии 2019-06-01.
* В команду `policy assignment create` добавлен параметр `--enforcement-mode`.

### <a name="storage"></a>Память

* В команду `az storage copy` добавлен параметр `--blob-type`.

## <a name="september-10-2019"></a>10 сентября 2019 г.

### <a name="acr"></a>ACR

* Добавлена группа команд `acr config retention` для настройки политики хранения.

### <a name="aks"></a>AKS

* Добавлена возможность интеграции ACR с помощью следующих команд:
  * В `aks [create|update]` добавлен параметр `--attach-acr` для подключения ACR к кластеру AKS.
  * В `aks update` добавлен параметр `--detach-acr` для отключения ACR от кластера AKS.

### <a name="arm"></a>ARM

* Добавлена возможность использования API версии 2019-05-10.

### <a name="batch"></a>Пакетная служба Azure

* Добавлены новые параметры конфигурации JSON в `--json-file` для `batch pool create`:
  * Добавлен параметр `MountConfigurations` для подключений файловой системы (дополнительные сведения см. в разделе https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body ).
  * Добавлено необязательное свойство `publicIPs` в `NetworkConfiguration` для общедоступных IP-адресов в пулах (дополнительные сведения см. в разделе https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body ).
* В `--image` добавлена поддержка коллекций общих образов.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Значение по умолчанию для `--start-task-wait-for-success` в `batch pool create` изменено на `true`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Значение по умолчанию для `Scope` в `AutoUserSpecification` задано как Pool (ранее `Task` на узлах Windows и `Pool` на узлах Linux).
  * Этот аргумент можно задать только в конфигурации JSON с помощью `--json-file`.

### <a name="hdinsight"></a>HDInsight

* Выпуск общедоступной версии
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр `--workernode-count/-c` в `az hdinsight resize` теперь является обязательным.

### <a name="key-vault"></a>Key Vault

* Исправлена проблема, когда подсети не удавалось удалить из сетевых правил.
* Исправлена проблема, когда дублированные подсети и IP-адреса могли быть добавлены к сетевым правилам.

### <a name="network"></a>Сеть

* Добавлен параметр `--interval` в `network watcher flow-log` для выбора значения интервала анализа трафика.
* Добавлена команда `network application-gateway identity` для управления удостоверением шлюза.
* Добавлена возможность указать идентификатор Key Vault в команде `network application-gateway ssl-cert`.
* Добавлена команда `network express-route peering peer-connection [show|list]`.

### <a name="policy"></a>Политика

* Добавлена возможность использования API версии 2019-01-01.

## <a name="august-27-2019"></a>27 августа 2019 г.

Версия 2.0.72

### <a name="acr"></a>ACR

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Прекращена поддержка SKU `classic`.

### <a name="api-management"></a>Управление API

* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена группа команд `apim`.

### <a name="appservice"></a>AppService

* Исправлена проблема с командой `webapp webjob continuous start` при указании слота.
* Изменена команда `webapp up` для обнаружения и удаления папки `env` из файла, используемого для развертывания.

### <a name="keyvault"></a>Хранилище ключей

* Исправлена ошибка в команде `keyvault secret set`, которая игнорировала аргумент `--expires`.

### <a name="network"></a>Сеть

* Добавлена поддержка IPv6-адресов для аргументов `--private-ip-address-version`.
* Добавлены новые команды `network private-endpoint [create|update|list-types]` для управления закрытыми конечными точками.
* Добавлена группа команд для `network private-link-service`.
* Добавлены аргументы `--private-endpoint-network-policies` и `--private-link-service-network-policies` для команды `network vnet subnet update`

### <a name="rbac"></a>RBAC

* Исправлена проблема с `ad app update --homepage`, когда домашнюю страницу нельзя было обновить.

### <a name="servicefabric"></a>Service Fabric

* Добавлена поддержка имен Key Vault в смешанном регистре.
* Исправлена проблема с использованием сертификатов в Key Vault.
* Исправлена проблема с использованием файлов сертификатов PFX.
* Исправлена проблема с `sf cluster certificate add`, когда группа ресурсов Key Vault не была указана.
* Исправлена проблема с неработающей командой `sf cluster set`.

### <a name="signalr"></a>SignalR

* Добавлены новые команды:
  * `signalr cors`: Управление CORS SignalR
  * `signalr restart`: Перезапуск службы SignalR
  * `signalr update`: Обновление службы SignalR
* Добавлен аргумент `--service-mode` для команды `signalr create`

### <a name="storage"></a>Память

* Добавлена команда `storage account revoke-delegation-keys`.

## <a name="august-13-2019"></a>13 августа 2019 г.

Версия 2.0.71

### <a name="appservice"></a>AppService

* Исправлена проблема, из-за которой выполнение команд `webapp webjob continuous` для слотов завершалось сбоем.

### <a name="botservice"></a>Служба Bot

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Прекращена поддержка создания ботов на основе пакета SDK версии 3.

### <a name="cognitiveservices"></a>Cognitive Services:

* Добавлены команды `cognitiveservices account network-rule`.

### <a name="cosmos-db"></a>Cosmos DB

* Удалено предупреждение при обновлении нескольких расположений для записи.
* Добавлены команды CRUD для ресурсов CosmosDB SQL, MongoDB, Cassandra, Gremlin и ресурсов таблиц, а также пропускной способности ресурсов.

### <a name="hdinsight"></a>HDInsight

Этот выпуск содержит большое число критических изменений.

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименованы параметры для `hdinsight create`:
  * Переименование `--storage-default-container` в `--storage-container`
  * Переименование `--storage-default-filesystem` в `--storage-filesystem`
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменен аргумент `--name` для `application create`, чтобы представлять имя приложения вместо имени кластера.
* Добавлен аргумент `--cluster-name` для `application create`, чтобы заменить старый аргумент `--name`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименованы параметры для `application create`:
  * Переименование `--application-type` в `--type`
  * Переименование `--marketplace-identifier` в `--marketplace-id`
  * Переименование `--https-endpoint-access-mode` в `--access-mode`
  * Переименован аргумент `--https-endpoint-destination-port` на `--destination-port`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалены параметры для `application create`:
  * `--https-endpoint-location`
  * `--https-endpoint-public-port`
  * `--ssh-endpoint-destination-port`
  * `--ssh-endpoint-location`
  * `--ssh-endpoint-public-port`
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ] Переименован аргумент `--target-instance-count` на `--workernode-count` для `hdinsight resize`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены все команды в группе `hdinsight script-action`, чтобы использовать параметр `--name` в качестве имени действия скрипта.
* Добавлен аргумент `--cluster-name` для всех команд `hdinsight script-action`, чтобы заменить старый аргумент `--name`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименован аргумент `--script-execution-id` на `--execution-id`для всех команд `hdinsight script-action`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `hdinsight script-action show` переименована в `hdinsight script-action show-execution-details`.
* [КРИТИЧЕСКОЕ ИЗМЕНЕНИЕ] Изменены параметры для `hdinsight script-action execute --roles`, чтобы они разделялись пробелами, а не запятыми.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--persisted` для `hdinsight script-action list`.
* Изменен параметр `hdinsight create --cluster-configurations`, чтобы принимать путь к локальному файлу JSON или строке JSON.
* Добавлена команда `hdinsight script-action list-execution-history`.
* Изменен параметр `hdinsight monitor enable --workspace`, чтобы принимать идентификатор или имя рабочей области Log Analytics.
* Добавлен аргумент `hdinsight monitor enable --primary-key`, который требуется, если в качестве параметра указан идентификатор рабочей области.
* Добавлены дополнительные примеры и обновленные описания для справочных сообщений.

### <a name="interactive"></a>Интерактивно

* Исправлена ошибка загрузки.

### <a name="kubernetes"></a>Kubernetes

* Теперь используется `https`, если порт контейнера панели мониторинга использует `https`.

### <a name="network"></a>Сеть

* Добавлен аргумент `--yes` для `network dns record-set cname delete`.

### <a name="profile"></a>Профиль

* Добавлен аргумент `--resource-type` для `account get-access-token`, чтобы получать маркеры доступа к ресурсам.

### <a name="servicefabric"></a>Service Fabric

* Добавлены все поддерживаемые версии ОС для команды sf cluster create.
* Исправлена ошибка проверки основного сертификата.

### <a name="storage"></a>Память

* Добавлена команда `storage copy`.

## <a name="july-30-2019"></a>30 июля 2019 г.

Версия 2.0.70

### <a name="acr"></a>ACR

* Исправлена проблема № 9952 (регрессия в команде `acr pack build`).
* Удалено имя образа построителя по умолчанию в `acr pack build`.

### <a name="appservice"></a>Служба приложений

* Команда `webapp config ssl` изменена для отображения сообщения, если ресурс не найден.
* Исправлена проблема, когда команда `functionapp create` не принимала тип учетной записи хранения `Standard_RAGRS`.
* Исправлена проблема, когда команда `webapp up` завершала работу со сбоем в случае выполнения со старой версией Python.

### <a name="network"></a>Сеть

* Удален недопустимый параметр `--ids` из `network nic ip-config add` (исправление проблемы № 9861).
* Исправлена проблема № 9604. Добавлен параметр `--root-certs` в `network application-gateway http-settings [create|update]` для поддержки связанных с пользователем доверенных корневых сертификатов.
* Исправлен аргумент `--subscription` для `network dns record-set ns create` (проблема № 9965).

### <a name="rbac"></a>RBAC

* Добавлена команда `user update`.
* [УСТАРЕЛО]`--upn-or-object-id` не рекомендуется использовать в связанных с пользователями командах.
    * Вместо этого применяйте аргумент `--id`.
* Добавлен аргумент `--id` в связанные с пользователями команды.

### <a name="sql"></a>SQL

* Добавлены команды управления для ключей и предохранителя TDE управляемого экземпляра.

### <a name="storage"></a>Память

* Добавлена команда `storage remove`.
* Исправлена проблема с `storage blob update`.

### <a name="vm"></a>ВМ

* Изменена команда `list-skus` для использования новой версии API для вывода сведений о зонах.
* Изменено значение по умолчанию параметра `--single-placement-group` на `false` для команды `vmss create`.
* Добавлена возможность выбирать номера SKU хранилища ZRS для `[snapshot|disk] create`.
* Добавлена новая группа команд `vm host` для поддержки выделенных узлов.
* Добавлены параметры `--host` и `--host-group` в команде `vm create` для указания выделенного узла виртуальной машины.

## <a name="july-16-2019"></a>16 июля 2019 г.

Версия 2.0.69

### <a name="appservice"></a>Служба приложений

* Изменены команды `webapp identity`. Теперь они возвращают правильное сообщение об ошибке, если параметр ResourceGroupName или имя приложения недопустимы.
* Исправлена команда `webapp list`. Теперь она возвращает правильное значение для параметра numberOfSites, если не указан параметр ResourceGroup.
* Исправлены побочные эффекты для команд `appservice plan create` и `webapp create`.

### <a name="core"></a>Основные сведения

* Исправлена ошибка, при которой отображался параметр `--subscription`, хотя он был неприменим.

### <a name="batch"></a>Пакетная служба Azure

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `batch pool node-agent-skus list` заменена на `batch pool supported-images list`.
* Добавлена поддержка правил безопасности, которые блокируют сетевой доступ к пулу на основе исходного порта трафика при использовании параметра `--json-file` команды `batch pool create network`.
* Добавлена поддержка выполнения задачи в рабочей папке контейнера или рабочей папке задачи пакета при использовании параметра `--json-file` команды `batch task create`.
* Исправлена ошибка в параметре `--application-package-references` команды `batch pool create`, которая позволяла работать только со значениями по умолчанию.

### <a name="eventhubs"></a>Концентраторы событий

* Добавлена проверка параметра `--rights` команд `authorizationrule`.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлен необязательный параметр для указания номера SKU реплики в команде создания реплики.
* Исправлена ошибка теста CI при создании реплики MySQL.

### <a name="relay"></a>Ретрансляция

* Исправлена проблема с гибридным подключением при выключенной авторизации клиента ([8775](https://github.com/azure/azure-cli/issues/8775)).
* Добавлен параметр `--requires-transport-security` для команды `relay wcfrelay create`.

### <a name="servicebus"></a>Служебная шина

* Добавлена проверка параметра `--rights` команд `authorizationrule`.

### <a name="storage"></a>Память

* Добавлена возможность обновления учетной записи хранения для AADDS в службе "Файлы".
* Устранена проблема, описанная здесь: `storage blob service-properties update --set`.

## <a name="july-2-2019"></a>2 июля 2019 г.

Версия 2.0.68

### <a name="core"></a>Основные сведения

* Командные модули теперь объединены в один распространяемый пакет Python. В результате этого прекращается непосредственное использование множества пакетов `azure-cli-` в PyPI.
  Это также должно уменьшить размер установки и повлияет только на пользователей, которые выполняли установку непосредственно через `pip`.

### <a name="acr"></a>ACR

* В заданиях добавлена поддержка триггеров таймера.

### <a name="appservice"></a>Служба приложений

* Внесены изменения в `functionapp create` для включения Application Insights по умолчанию.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена устаревшая команда `functionapp devops-build`.
  *  Вместо нее используйте новую команду `az functionapp devops-pipeline`.
* В `functionapp deployment config-zip` добавлена поддержка плана потребления приложения-функции Linux.

### <a name="cosmos-db"></a>Cosmos DB

* Добавлена возможность отключения TTL.

### <a name="dls"></a>DLS

* Обновленная версия ADLS (0.0.45)

### <a name="feedback"></a>Отзывы

* При сообщении о сбое при выполнении команды расширения `az feedback` теперь пытается открыть браузер по URL-адресу репозитория или проекта расширения из индекса.

### <a name="hdinsight"></a>HDInsight

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Имя группы команд `oms` изменилось на `monitor`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр `--http-password/-p` стал обязательным. 
* Добавлены средства заполнения для `--cluster-admin-account` и средство заполнения для параметров `cluster-users-group-dns`. 
* Параметр `cluster-users-group-dns` стал обязательным, если присутствует `—esp`.
* Добавлено время ожидания для всех существующих средств автоматического заполнения аргументов.
* Добавлено время ожидания для преобразования имени ресурса в идентификатор ресурса.
* Внесены изменения в средства автоматического заполнения для выбора ресурсов из любой группы ресурсов. Это может быть группа ресурсов, отличная от той, которая указана с помощью `-g`.
* Добавлена поддержка параметров `--sub-domain-suffix` и `--disable_gateway_auth` в команде `hdinsight application create`.

### <a name="managed-services"></a>Управляемые службы

* Командный модуль управляемых служб выпущен в предварительной версии.

### <a name="profile"></a>Профиль
* Аргумент `--subscription` подавляется для команды выхода.

### <a name="rbac"></a>RBAC

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален аргумент `--password` для `create-for-rbac`.
* В команду `create` добавлен параметр `--assignee-principal-type` для устранения периодических сбоев из-за задержки репликации сервера AAD Graph.
* Исправлен сбой в `ad signed-in-user` при перечислении собственных объектов.
* Исправлена проблема, при которой `ad sp` не удавалось найти нужное приложение в субъекте-службе.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлена поддержка репликации MariaDB.

### <a name="sql"></a>SQL

* Описаны допустимые значения для `sql db create --sample-name`.

### <a name="storage"></a>Память

* В `storage blob generate-sas` добавлена поддержка маркера SAS для делегирования пользователя с помощью `--as-user`. 
* В `storage container generate-sas` добавлена поддержка маркера SAS для делегирования пользователя с помощью `--as-user`. 

### <a name="vm"></a>ВМ

* Исправлена ошибка, при которой команда `vmss create` возвращала сообщение об ошибке при выполнении с `--no-wait`.
* Прекращена проверка `vmss create --single-placement-group` на стороне клиента. Команда не завершается сбоем, если для `--single-placement-group` задано значение `true`, а значение `--instance-count` больше 100 или указаны зоны доступности. Сама проверка теперь выполняется службой вычислений.
* Исправлена ошибка, при которой команда `[vm|vmss] extension image list` завершалась сбоем при указании `--latest`.


## <a name="june-18-2019"></a>18 июня 2019 г.

Версия 2.0.67

### <a name="core"></a>Основные сведения

В этом выпуске добавлен новый тег [Предварительная версия], который яснее дает понять, что группа команд, команда или аргумент находятся в состоянии предварительной версии. Ранее это указывалось в тексте справки или подразумевалось в номере версии командного модуля.
В будущем в интерфейсе командной строки номера версий отдельных пакетов не будут указываться. Если команда доступна в предварительной версии, это также касается и всех ее аргументов. Если группа команд помечается как находящаяся в предварительной версии, значит все команды и аргументы также считаются доступными в предварительной версии.

В результате этого изменения несколько групп команд могут "внезапно" оказаться в состоянии предварительной версии в этом выпуске. В действительности большинство пакетов, которые находились в состоянии предварительной версии, в этом выпуске будут считаться общедоступными.

### <a name="acr"></a>ACR
* Добавлена команда acr check-health.
* Улучшена обработка ошибок с маркерами безопасности AAD и ошибок при получении внешних команд.

### <a name="acs"></a>ACS
* Устаревшие команды ACS теперь скрыты в тексте справки.

### <a name="ams"></a>AMS
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.], состоящее в возврате строк времени ISO 8601 для archive-window-length и key-frame-interval-duration.

### <a name="appservice"></a>AppService
* Добавлена маршрутизация на основе расположение для `webapp deleted list` и `webapp deleted restore`.
* Исправлена проблема, при которой целевой URL-адрес веб-приложения ("Вы можете запустить приложение в...") не был активным в Azure Cloud Shell.
* Устранена проблема, при которой создание приложений в некоторых SKU завершалось сбоем с ошибкой AlwaysOn.
* Добавлена предварительная проверка в `[appservice|webapp] create`.
* Исправлено `[webapp|functionapp] traffic-routing` для использования правильного actionHostName.
* Добавлена поддержка слотов для команд `functionapp`.

### <a name="batch"></a>Пакетная служба Azure
* Исправлена регрессия проверки подлинности AAD, которую вызывал чрезмерный поток уведомлений об авторизации с помощью общего ключа.

### <a name="batchai"></a>Batch AI
* Команды BatchAI теперь признаны устаревшими и скрыты.

### <a name="botservice"></a>Служба Bot
* Добавлены предупреждающие сообщения о прекращении поддержки и режиме обслуживания для команд, которые поддерживают пакет SDK версии 3.

### <a name="cosmosdb"></a>Cosmos DB
* [УСТАРЕЛО] использовать команду `cosmosdb list-keys`.
* Добавлена команда `cosmosdb keys list`, заменяющая `cosmosdb list-keys`.
* `cosmsodb create/update`: Добавлен новый формат для --location, который позволяет задавать свойство isZoneRedundant. Нерекомендуемый старый формат.

### <a name="eventgrid"></a>Сетка событий
* Добавлены команды `eventgrid domain` для операций CRUD домена.
* Добавлены команды `eventgrid domain topic` для операций CRUD разделов домена.
* В `eventgrid [topic|event-subscription] list` добавлен аргумент `--odata-query` для фильтрации результатов с использованием синтаксиса OData.
* `event-subscription create/update`: Добавлена ServiceBusQueue в качестве новых значений для параметра `--endpoint-type`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.], состоящее в прекращении поддержки `--included-event-types All` с `eventgrid event-subscription [create|update]`.

### <a name="hdinsight"></a>HDInsight
* Добавлена поддержка параметра `--ssh-public-key` в команде `hdinsight create`.

### <a name="iot"></a>Интернет вещей
* Добавлена поддержка для повторного создания ключей политики авторизации.
* Добавлен пакет SDK и поддержка для службы подготовки репозитория DigitalTwin.

### <a name="network"></a>Сеть
* Добавлена поддержка зон для Шлюза NAT.
* Добавлена команда `network list-service-tags`.
* Исправлена проблема с `dns zone import`, при которой пользователям не удавалось импортировать записи А с подстановочным знаком.
* Исправлена проблема с `watcher flow-log configure`, при которой не удавалось включить ведение журнала потоков в определенных регионах.

### <a name="resource"></a>Ресурс
* Добавлена команда `az rest` для вызовов REST.
* Исправлена ошибка, возникавшая при использовании `policy assignment list` с группой ресурсов или уровнем подписки `--scope`.

### <a name="servicebus"></a>Служебная шина
* Устранена проблема № [9319](https://github.com/azure/azure-cli/issues/9319) с `servicebus topic create --max-size`.

### <a name="sql"></a>SQL
* Параметр `--location` стал необязательным для `sql [server|mi] create`. Если он не указан, используется расположение группы ресурсов.
* Исправлена ошибка "Объект NoneType не подлежит итерации" с `sql db list-editions --available`.

### <a name="sqlvm"></a>SQLVm
* Критическое изменение. Для `sql vm create` теперь требуется параметр `--license-type`.
* Внесены изменения, позволяющие задавать SKU образа SQL при создании или обновлении виртуальной машины SQL.

### <a name="storage"></a>Память
* Исправлена проблема с отсутствующим ключом учетной записи для `storage container generate-sas`.
* Исправлена проблема с `storage blob sync` на платформе Linux.

### <a name="vm"></a>ВМ
* [Предварительная версия] Добавлены команды `vm image template` для создания образов виртуальных машин.

## <a name="june-4-2019"></a>4 июня 2019 г.

Версия 2.0.66

### <a name="core"></a>Основные сведения
* Исправлена ошибка, из-за которой выполнение команды завершалось со сбоем, если параметр `--output yaml` использовался с параметром `--query`

### <a name="acr"></a>ACR
* Добавлена группа команд acr pack для создания заданий быстрой сборки с помощью пакетов сборок.

### <a name="acs"></a>ACS
* Разрешено включение и отключение надстройки панели мониторинга Kubernetes для AKS.
* Если подписку нельзя использовать с Azure Red Hat OpenShift, будет отображаться соответствующее сообщение.

### <a name="batch"></a>Пакетная служба Azure
* Улучшена обработка ошибок, если не выполнен вход в учетную запись \[[№ 9165](https://github.com/Azure/azure-cli/issues/9165)\]\[[№ 8978](https://github.com/Azure/azure-cli/issues/8978)\].

### <a name="iot"></a>Интернет вещей
* Добавлена поддержка для перехода на другой ресурс вручную.

### <a name="network"></a>Сеть
* Добавлены команды `network application-gateway waf-policy` для поддержки настраиваемых правил WAF.
* Добавлены аргументы `--waf-policy` и `--max-capacity` для команды `network application-gateway [create|update]` 

### <a name="resource"></a>Ресурс
* Улучшен вывод сообщения команды `deployment create` при отсутствии TTY.

### <a name="role"></a>Роль
* Обновлен текст справки.

### <a name="compute"></a>Службы вычислений
* Добавлена поддержка команды `vm create` для создания виртуальных машин из управляемого образа с номерами LUN дисков данных, которые не начинаются с 0 или для которых пропущены номера.

## <a name="may-21-2019"></a>21 мая 2019 г.

Версия 2.0.65

### <a name="core"></a>Основные сведения
* Улучшена функция обратной связи при ошибках аутентификации.
* Исправлена проблема, из-за которой интерфейс командной строки загружал расширения, которые не были совместимы с его базовой версией.
* Исправлена проблема с запуском и неисправностью `clouds.config`.

### <a name="acr"></a>ACR
* Добавлена поддержка управляемых удостоверений в Задачи.

### <a name="acs"></a>ACS
* Исправлена команда `openshift create`, используемая с пользовательским клиентом AAD.

### <a name="appservice"></a>AppService
* [УСТАРЕЛО] Команда `functionapp devops-build` устарела и будет удалена в следующем выпуске.
* Внесено изменение в `functionapp devops-pipeline` для получения журнала сборки из Azure DevOps в режиме подробного протоколирования.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален неподдерживаемый флаг `--use_local_settings` из команды `functionapp devops-pipeline`.
* Внесено изменение в `webapp up` для возврата выходных данных JSON, если `--logs` не используется.
* Добавлена поддержка записи ресурсов по умолчанию в локальную конфигурацию для `webapp up`.
* Добавлена поддержка `webapp up` для повторного развертывания приложения без использования аргумента `--location`.
* Устранена проблема, из-за которой нельзя было создать для Linux номер SKU с планом службы приложений "Бесплатный".

### <a name="botservice"></a>Служба Bot
* Внесено изменение для включения поддержки всех регистров в параметрах `--lang` для команд.
* Обновлено описание модуля команды.

### <a name="consumption"></a>Потребление
* Добавлен отсутствующий обязательный параметр при выполнении `consumption usage list --billing-period-name`.

### <a name="iot"></a>Интернет вещей
* Добавлена поддержка перечисления всех ключей.

### <a name="network"></a>Сеть
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]: Removed `network interface-endpoints` command group - use `network private-endpoints` 
* Добавлен аргумент `--nat-gateway` для `network vnet subnet [create|update]` для подключения к шлюзу NAT.
* Исправлена проблема с `dns zone import`, из-за которой имена записей не сопоставлялись с типом записей.

### <a name="rdbms"></a>Реляционная СУБД
* Добавлена поддержка Postgres и MySQL для георепликации.

### <a name="rbac"></a>RBAC
* Добавлена поддержка области группы управления для `role assignment`.

### <a name="storage"></a>Память
* `storage blob sync`: добавьте команду синхронизации для хранилища BLOB-объектов.

### <a name="compute"></a>Службы вычислений
* Добавлен параметр `--computer-name` для `vm create` для установки имени виртуальной машины.
* Переименован параметр `--ssh-key-value` в `--ssh-key-values` для `[vm|vmss] create` для приема нескольких значений пути или открытого ключа SSH.
  * __Примечание.__ Это **не** критическое изменение, благодаря которому `--ssh-key-value` будет правильно анализироваться, так как соответствует только `--ssh-key-values`.
* Внесено изменение в аргумент `ppg create` для `--type` — теперь он необязательный.

## <a name="may-6-2019"></a>6 мая 2019 г.

Версия 2.0.64

### <a name="acs"></a>ACS
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален флаг `--fqdn` из команд `openshift`.
* Внесено изменение для использования общедоступной версии API для Azure Red Hat Openshift.
* Добавлен флаг `customer-admin-group-id` в `openshift create`.
* [Общедоступная версия.] `(PREVIEW)` больше не используется для `aks create` в параметре `--network-policy`.

### <a name="appservice"></a>Служба приложений
* [УСТАРЕЛО] Команда `functionapp devops-build` отмечена как нерекомендуемая.
  * Команда переименована в `functionapp devops-pipeline`.
* Исправлен процесс получения правильного имени пользователя для Cloud Shell, приводивший к ошибке выполнения `webapp up`.
* Обновлена документация по `appservice plan --sku` в соответствии с поддерживаемыми планами службы приложений.
* Добавлены необязательные аргументы для группы ресурсов и план для `webapp up`.
* Добавлена поддержка `webapp ssh` в соответствии с переменной среды `AZURE_CLI_DISABLE_CONNECTION_VERIFICATION`.
* Добавлена поддержка `appserviceplan create` для ценовой категории "Бесплатный" в Linux.
* Внесено изменение в `webapp up` для перевода в спящий режим на 30 с после установки параметра приложения `SCM_DO_BUILD_DURING_DEPLOYMENT=true` для обработки холодного запуска Kudu.
* Добавлена поддержка среды выполнения `powershell` для `functionapp create` в Windows.
* Добавлена команда `create-remote-connection`.

### <a name="batch"></a>Пакетная служба Azure
* Исправлена ошибка в проверяющем элементе управления для параметров `--application-package-references`.

### <a name="botservice"></a>Служба Bot
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `bot create -v v4 -k webapp` для создания пустого бота веб-приложения по умолчанию (т. е. бот не развертывается в Службе приложений).
* Добавлен флаг `--echo` в `bot create` для использования старого поведения с `-v v4`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменено значение по умолчанию `--version` на `v4`.
  * __ПРИМЕЧАНИЕ.__ `bot prepare-publish` по-прежнему использует старое значение по умолчанию.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `--lang` — значение `Csharp` больше не является значением по умолчанию. Если команда требует `--lang`, который не указан, команда завершит работу с ошибкой.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в аргументы `--appid` и `--password` для `bot create` — теперь они являются обязательными и их можно создать с помощью `ad app create`.
* Добавлена проверка `--appid` и `--password`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `bot create -v v4`, чтобы не создавать или не использовать учетную запись хранения или Application Insights.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `bot create -v v3`, чтобы требовать регион, где доступна служба Application Insights.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `bot update`, чтобы влиять только на определенные свойства бота.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в флаг `--lang` для принятия `Javascript` вместо `Node`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]`Node` больше не используется как допустимое значение `--lang`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `bot create -v v4 -k webapp` — значение `SCM_DO_BUILD_DURING_DEPLOYMENT` больше не равно true. Все развертывания через Kudu будут работать в соответствии с поведением по умолчанию.
* Внесено изменение в `bot download` для ботов без файлов `.bot`, чтобы создавать файл конфигурации для определенного языка со значениями из параметров приложения для бота.
* В команду `bot prepare-deploy` добавлена поддержка параметра `Typescript`.
* Добавлено предупреждающее сообщение в `bot prepare-deploy` для ботов `Javascript` и `Typescript`, когда `--code-dir` не содержит `package.json`.
* Внесено изменение в `bot prepare-deploy` для возврата `true` при успешном выполнении.
* Включено ведение подробного журнала для `bot prepare-deploy`.
* Добавлены более доступные регионы Application Insights для `az bot create -v v3`.

### <a name="configure"></a>Configure
* Добавлена поддержка конфигурации по умолчанию для аргумента на основе папки.

### <a name="eventhubs"></a>Концентраторы событий
* Добавлены команды `namespace network-rule`.
* Добавлен аргумент `--default-action` для правил сети для `namespace [create|update]`.

### <a name="network"></a>Сеть
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменен аргумент `--cache` на `--defer` для `vnet [create|update]`. 

### <a name="policy-insights"></a>Анализ политик
* Добавлена поддержка `--expand PolicyEvaluationDetails` для результатов оценки политики запросов для ресурса.

### <a name="role"></a>Роль
* [УСТАРЕЛО] Внесено изменение в `create-for-rbac` для скрытия аргумента --password (поддержка прекращается в мае 2019 г.).

### <a name="service-bus"></a>Служебная шина
* Добавлены команды `namespace network-rule`.
* Добавлен аргумент `--default-action` для правил сети для `namespace [create|update]`.
* Внесено исправление в `topic [create|update]` — теперь `--max-size` поддерживает значения 10, 20, 40 и 80 ГБ для номера SKU ценовой категории "Премиум".

### <a name="sql"></a>SQL
* Добавлены команды `sql virtual-cluster [list|show|delete]`.

### <a name="vm"></a>ВМ
* Добавлены параметры `--protect-from-scale-in` и `--protect-from-scale-set-actions` для `vmss update`, чтобы включать обновления политики защиты для экземпляров виртуальных машин из Масштабируемого набора виртуальных машин.
* Добавлены параметры `--instance-id` для `vmss update` для включения общего обновления экземпляров виртуальных машин из Масштабируемого набора виртуальных машин.
* Добавлен параметр `--instance-id` для команды `vmss wait`.
* Добавлена новая группа команд `ppg` для управления группами размещения близкого взаимодействия.
* Добавлен параметр `--ppg` для `[vm|vmss] create` и `vm availability-set create` для управления PPG.
* Добавлен параметр `--hyper-v-generation` для команды `image create`.

## <a name="april-23-2019"></a>23 апреля 2019 г.

Версия 2.0.63

### <a name="acs"></a>ACS
* Внесено изменение в `aks get-credentials` для запроса на перезапись повторяющихся значений.
* Удалено описание `(PREVIEW)` из команд Dev Spaces aks use-dev-spaces и aks remove-dev-spaces.

### <a name="ams"></a>AMS
* Исправлена ошибка с обновлением фильтров ресурсов и учетных записей.

### <a name="appservice"></a>AppService
* Добавлена поддержка ASE и времени ожидания для `webapp ssh`.
* Добавлена возможность настройки непрерывной интеграции и развертывания в конвейере Azure DevOps из репозитория Github для приложений-функций.
* Добавлен аргумент `--github-pat` для `functionapp devops-build create` для получения личного маркера доступа Github.
* Добавлен аргумент `--github-repository` для `functionapp devops-build create` для подключения репозитория Github, который содержит исходный код приложения-функции.
* Исправлена проблема со сбоем `az webapp up --logs` и обновлением .Net Core до версии 2.1 по умолчанию.
* Удалены ненужные параметры приложения-функции при создании приложения-функции с помощью плана потребления.
* Внесены изменения в `webapp up`, чтобы строка ASP по умолчанию добавляла номера в конец для создания плана службы приложений на основе параметров SKU.
* Добавлен параметр `-b` для `webapp up` для запуска приложения в браузере.
* Внесены изменения в `webapp deployment source config zip` для обработки переменной среды `AZURE_CLI_DISABLE_CONNECTION_VERIFICATION`.

### <a name="deployment-manager"></a>Диспетчер развертывания
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Создание и администрирование артефактов, которые поддерживают развертывания

### <a name="lab"></a>Лаборатория
* Исправлена ошибка, которая приводила к неожиданному завершению работы.

### <a name="network"></a>Сеть
* Добавлено автоматическое делегирование сервера имен для `dns zone create` в родительском объекте во время создания дочерней зоны.

### <a name="resource"></a>Ресурс
* [УСТАРЕЛО] Не рекомендуются к использованию аргументы `--link-id`, `--target-id` и `--filter-string` для `resource link`.
  * Используйте вместо них аргументы `--link`, `--target` и `--filter`.
* Исправлена проблема, из-за которой команды `resource link [create|update]` не работали.
* Исправлена проблема, из-за которой удаление с помощью идентификатора ресурса приводило к сбою или ошибке.

### <a name="sql"></a>SQL
* Добавлена поддержка пользовательского часового пояса в управляемых экземплярах.
* Внесено изменение, чтобы разрешить использовать имя эластичного пула с `sql db update`.
* В команду `sql server [create|update]` добавлена поддержка параметра `--no-wait`.
* Добавлена команда `sql server wait`.

### <a name="storage"></a>Память
* Устранена проблема с двойной кодировкой маркеров SAS в `storage blob generate-sas`.

### <a name="vm"></a>ВМ
* Добавлен флаг `--skip-shutdown` для `vm|vmss stop` для выключения виртуальных машин без завершения работы.
* Добавлен аргумент `--storage-account-type` для `sig image-version create` настройки типа учетной записи профиля публикации.
* Добавлен аргумент `--target-regions` для `sig image-version create` для указания типов учетных записей хранения, связанных с регионами.

## <a name="april-9-2019"></a>9 апреля 2019 г.

### <a name="core"></a>Основные сведения
* Исправлена проблема, из-за которой для некоторых расширений отображалась версия `Unknown` и ее невозможно было обновить.

### <a name="acr"></a>ACR
* Добавлена поддержка запуска образа без контекста.

### <a name="ams"></a>AMS
* [УСТАРЕЛО]: Deprecated the `--bitrate` parameter of `account-filter` and `asset-filter`
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]: Renamed the `--bitrate` parameter to `--first-quality`
* Добавлена поддержка новых параметров шифрования в `ams streaming-policy create`.
* Добавлен новый параметр `--filters` для `ams streaming-locator create`.

### <a name="appservice"></a>AppService
* В команду `webapp up` добавлена поддержка параметра `--logs`.
* Исправлены ошибки в команде `functionapp devops-build create` при создании файла `azure-pipelines.yml`.
* Улучшена обработка и индикаторы ошибок с `unctionapp devops-build create`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален флаг `--local-git` для команды `devops-build`. Обнаружение и обработка локального репозитория Git являются обязательными для создания конвейеров DevOps в Azure.
* Добавлена поддержка создания плана функций Linux.
* Добавлена возможность менять план для приложения-функции с помощью `functionapp update --plan`.
* Добавлена поддержка параметров горизонтального увеличения масштаба плана "Премиум" для Функций Azure.

### <a name="cdn"></a>CDN
* Добавлена поддержка `Microsoft_Standard` и `Standard_ChinaCdn`.

### <a name="feedback"></a>Отзывы
* Внесены изменения в `feedback` для отображения метаданных недавно выполненных команд.
* Внесены изменения в `feedback`. Теперь при регистрации проблемы отображается запрос, в соответствии с которым пользователь должен открыть браузер и воспользоваться шаблоном проблемы.
* Внесены изменения в `feedback`. Теперь текст проблемы выводится при запуске с параметром --verbose.

### <a name="monitor"></a>Монитор
* Исправлена проблема, из-за которой у параметра count было недопустимое значение в `metrics alert [create|update]`. 

### <a name="network"></a>Сеть
* Исправлен формат таблицы, которая не отображалась с помощью `vnet-gateway list-bgp-peer-status`.
* Добавлены команды `list-request-headers` и `list-response-headers` для `application-gateway rewrite-rule`.
* Добавлена команда `list-server-variables` для `application-gateway rewrite-rule condition`.
* Исправлена проблема, из-за которой обновление состояния соединения на порту ExpressRoute вызывало неизвестное исключение атрибута `express-route port update`.

### <a name="privatedns"></a>Частная зона DNS
* Добавлена команда `network private-dns` для частных зон DNS.

### <a name="resource"></a>Ресурс
* Исправлена проблема с `deployment create` и `group deployment create`, из-за которой файл параметров с пустым набором параметров не работал.

### <a name="role"></a>Роль
* Внесены исправления в `create-for-rbac`. Теперь `--years` обрабатывается правильно.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесены изменения в `role assignment delete`. Теперь появляется запрос при удалении всех назначений в рамках подписки без дополнительных условий.

### <a name="sql"></a>SQL
* Команда `sql mi [create|update]` обновлена с помощью свойств proxyOverride и publicDataEndpointEnabled.

### <a name="storage"></a>Память
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален результат выполнения `storage blob delete`.
* В команду `storage blob generate-sas` добавлен параметр `--full-uri` для создания полного URI большого двоичного объекта с помощью SAS.
* В команду `storage file copy start` добавлен параметр `--file-snapshot` для копирования файла из моментального снимка.
* Внесены изменения в `storage blob copy cancel`. Теперь вместо исключения для NoPendingCopyOperation отображается только сообщение об ошибке.

## <a name="march-26-2019"></a>26 марта 2019 г.


### <a name="core"></a>Основные сведения
* Устранены проблемы с несовместимостью расширений для разработки.
* Функция обработки ошибок теперь указывает клиентам на страницу проблем.

### <a name="cloud"></a>Cloud
* Исправлена ошибка с сообщением о не найденной подписке в `cloud set`.

### <a name="acr"></a>ACR
* Исправлена ошибка с избыточными источниками при импорте изображений.
* Добавлено `--auth-mode` в команды `acr build`, `acr run`, `acr task create` и `acr task update`.
* Добавлена команда acr task credential для управления учетными данными для задачи.
* Добавлено --no-wait в команду `acr build`.

### <a name="appservice"></a>AppService
* Исправлена ошибка с `webapp up`, из-за которой нельзя было правильно выполнить запуск из пустого каталога или скрипта неизвестного кода.
* Исправлена ошибка, из-за которой не работали слоты для `[webapp|functionapp] config ssl bind`.

### <a name="bot-service"></a>Служба Bot
* Добавлено `bot prepare-deploy` для подготовки к развертыванию ботов с помощью `webapp`.
* Изменено `bot create --kind registration` для отображения пароля, если пароль не указан.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменено `--endpoint` в `bot create --kind registration` на пустую строку (по умолчанию) вместо запрашиваемой.
* Добавлено `SCM_DO_BUILD_DURING_DEPLOYMENT` для параметров приложения шаблона ARM для ботов веб-приложений версии 4.

### <a name="cdn"></a>CDN
* Добавлена поддержка параметра `--no-wait` в команде `cdn endpoint [create|update|start|stop|delete|load|purge]`.  
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменено поведение кэширования строки запроса `cdn endpoint create` по умолчанию. IgnoreQueryString больше не используется по умолчанию. Это значение задает служба.

### <a name="cosmosdb"></a>Сosmos DB
* Добавлена поддержка `--enable-multiple-write-locations` для обновления учетной записи.
* Добавлена подгруппа `network-rule` с командами `add`, `remove` и `list` для управления правилами виртуальной сети учетной записи Cosmos DB.

### <a name="interactive"></a>Интерактивно
* Исправлена несовместимость с интерактивным расширением, установленным с помощью azdev.

### <a name="monitor"></a>Монитор
* Внесено изменение, разрешающее использовать значение измерения `*` для `monitor metrics alert [create|update]`.

### <a name="network"></a>Сеть
* Добавлена группа команд `rewrite-rule` для `application-gateway`.

### <a name="profile"></a>Профиль
* Включена поддержка учетной записи уровня клиентов для управляемого удостоверения службы для `login`.

### <a name="postgres"></a>Postgres 
* Добавлены команды postgresql `replica` и команда `restart server`.
* Внесены изменения для получения расположения по умолчанию из группы ресурсов, когда оно не предоставляется при создании серверов, и добавления проверки числа дней хранения.

### <a name="resource"></a>Ресурс
* Улучшены табличные выходные данные для `deployment [create|list|show]`.
* Исправлена проблема с `deployment [create|validate]`, из-за которой secureObject типа не распознавался.

### <a name="graph"></a>График
* Добавлена поддержка параметра `--end-date` в команде `ad [app|sp] credential reset`.
* Добавлена поддержка разрешений для `ad app permission add`.
* Исправлена проблема с `ad app permission list`, из-за которой отсутствовали разрешения.
* Изменено `ad sp delete` для пропуска удаления назначения роли, если текущая учетная запись не содержит подписок.
* Изменено `ad app create` для использования `--identifier-uris` пустого списка (по умолчанию), если список не указан.

### <a name="storage"></a>носителей.
* Добавлено `--snapshot` для `storage file download-batch` для скачивания из моментального снимка общего ресурса.
* Изменен индикатор выполнения `storage blob [download-batch|upload-batch]`, чтобы обобщить сведения и указать текущий большой двоичный объект.
* Исправлена проблема с `storage account update` при обновлении параметров шифрования.
* Исправлена проблема со сбоем `storage blob show` при использовании OAuth (`--auth-mode=login`).

### <a name="vm"></a>ВМ
* Добавлена команда `image update`.

## <a name="march-12-2019"></a>12 марта 2019 г.

Версия 2.0.60

### <a name="core"></a>Основные сведения

* Исправлена недопустимая ошибка в `cloud set` о том, что подписка не найдена.

### <a name="acr"></a>ACR

* Исправлена ошибка с избыточными источниками при импорте изображений.

### <a name="acs"></a>ACS

* Внесены изменения, в соответствии с которыми параметр `--listen-address` для `aks browse` игнорируется, если он не поддерживается kubectl. 

### <a name="appservice"></a>AppService

* Добавлено `[webapp|functionapp] deployment list-publishing-credentials` для получения URL-адреса публикации Kudu и связанных учетных данных.
* Удалена ошибочная инструкция печати для `webapp auth update`.
* Исправлено `functionapp` для настройки правильного образа для среды выполнения в планах службы приложений Linux.
* Удален тег предварительной версии для `webapp up` и добавлены усовершенствования в команду.

### <a name="botservice"></a>Служба Bot

* Добавлено `SCM_DO_BUILD_DURING_DEPLOYMENT` для параметров приложения шаблона ARM для ботов веб-приложений версии 4.
* Добавлено `Microsoft-BotFramework-AppId` и `Microsoft-BotFramework-AppPassword` для параметров приложения шаблона ARM для ботов веб-приложений версии 4.
* Удалены одинарные кавычки из выходных данных команды `bot publish` в конце `bot create`.
* Изменено `bot publish` для включения поддержки асинхронных операций.

### <a name="container"></a>Контейнер

* Добавлен аргумент `--no-wait` для команды `container [start|restart]`

### <a name="eventhub"></a>концентратор событий.

* Добавлен флаг `--skip-empty-archives` для `eventhub create|update` для включения поддержки пустых архивов при записи.

### <a name="find"></a>Поиск

* Основные обновления функций

### <a name="hdinsight"></a>HDInsight

* Добавлен параметр `--storage-account-managed-identity` для `hdinsight create` для включения поддержки MSI ADLS 2-го поколения.

### <a name="network"></a>Сеть

* Исправлена проблема с `vpn-connection update`, когда обновление VPN-подключения между шлюзами в разных подписках завершается ошибкой.

### <a name="rdbms"></a>Rdbms

* Незначительные исправления для получения расположения по умолчанию из группы ресурсов, когда оно не предоставляется при создании серверов, и добавления проверки числа дней хранения.

### <a name="role"></a>Роль

* Исправлено `role definition update` для использования идентификатора для правильного разрешения определения.
* Изменено `ad app credential reset` для исключения предположения о том, что субъект-служба приложения всегда существует.

### <a name="service-fabric"></a>Service Fabric

* Исправлена проблема с `sf cluster list` и невозможностью итерации.

## <a name="february-26-2019"></a>26 февраля 2019 г.

Версия 2.0.59

### <a name="core"></a>Основные сведения

* Исправлена проблема, из-за которой в некоторых экземплярах с использованием `--subscription NAME` выдавалось исключение.

### <a name="acr"></a>ACR

* Добавлен параметр `--target` для команд `acr build`, `acr task create` и `acr task update`.
* Улучшена обработка ошибок для команд среды выполнения без входа в Azure.

### <a name="acs"></a>ACS

* Добавлен параметр `--listen-address` для команды `aks port-forward`.

### <a name="appservice"></a>AppService

* Добавлена команда `functionapp devops-build`.

### <a name="batch"></a>Пакетная служба Azure
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена команда `batch pool upgrade os`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалено свойство `Pacakges` из ответов `Application`.
* Добавлена команда `batch application package list` для вывода списка пакетов приложения.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменено `--application-id` на `--application-name` во всех командах `batch application`. 
* Добавлен аргумент `--json-file` к командам для запрашивания необработанного ответа API.
* Обновлена проверка для автоматического включения `https://` во все конечные точки, если они отсутствуют.

### <a name="cosmosdb"></a>Cosmos DB

* Добавлена подгруппа `network-rule` с командами `add`, `remove` и `list` для управления правилами виртуальной сети учетной записи Cosmos DB.

### <a name="kusto"></a>Kusto

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Для типов `hot_cache_period` и `soft_delete_period` для базы данных изменен формат длительности ISO8601.

### <a name="network"></a>Сеть

* Добавлен аргумент `--express-route-gateway-bypass` для команды `vpn-connection [create|update]`
* Добавлены группы команд из расширения `express-route`.
* Добавлены группы команд `express-route gateway` и `express-route port`.
* Добавлен аргумент `--legacy-mode` в команду `express-route peering [create|update]`. 
* Добавлены аргументы `--allow-classic-operations` и `--express-route-port` для `express-route [create|update]`.
* Добавлен аргумент `--gateway-default-site` для команды `vnet-gateway [create|update]`
* Добавлены команды `ipsec-policy` для `vnet-gateway`.

### <a name="resource"></a>Ресурс

* Исправлена проблема с `deployment create`, из-за которой в поле типа учитывался регистр.
* Добавлена поддержка файла параметров на основе URI для `policy assignment create`.
* Добавлена поддержка определений и параметров на основе URI для `policy set-definition update`.
* Исправлена обработка параметров и правил для `policy definition update`.
* Исправлена проблема с `resource show/update/delete/tag/invoke-action`, из-за которой идентификаторы разных подписок не обрабатывали правильно идентификатор подписки.

### <a name="role"></a>Роль

* Добавлена поддержка ролей приложений для `ad app [create|update]`.

### <a name="vm"></a>ВМ

* Исправлена проблема с отсутствием возможности включения `vm create where `--accelerated-networking` по умолчанию для Ubuntu 18.0.

## <a name="february-12-2019"></a>12 февраля 2019 г.

Версия 2.0.58

### <a name="core"></a>Основные сведения

* `az --version` теперь отображает уведомление при наличии пакетов, которые можно обновить.
* Исправлена регрессия, при которой `--ids` нельзя было больше использовать с выходными данными JSON.

### <a name="acr"></a>ACR
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена группа команд `acr build-task`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Из `acr repository delete` удалены параметры `--tag` и `--manifest`.

### <a name="acs"></a>ACS
* `aks [enable-addons|disable-addons]` теперь поддерживает имена без учета регистра.
* Добавлена поддержка операции обновления Azure Active Directory с помощью `aks update-credentials --reset-aad`.
* Добавлено пояснение, что значение `--output` для `aks get-credentials` игнорируется.

### <a name="ams"></a>AMS
* Добавлены команды `ams streaming-endpoint [start | stop | create | update] wait`.
* Добавлены команды `ams live-event [create | start | stop | reset] wait`.

### <a name="appservice"></a>Служба приложений
* Добавлена возможность создавать и настраивать функции с помощью контейнеров ACR.
* Добавлена поддержка обновления конфигураций веб-приложений с помощью JSON.
* Улучшена справка для `appservice-plan-update`.
* Добавлена поддержка App Insights при создании приложений-функций.
* Устранены проблемы с SSH для веб-приложений.

### <a name="botservice"></a>Служба Bot
* Улучшен пользовательский интерфейс для `bot publish`.
* Добавлены предупреждения для времени ожидания при выполнении `npm install` во время операции `az bot publish`.
* Удален недопустимый символ `.` из значения `--name` в `az bot create`.
* Имена ресурсов больше не выбираются в случайном порядке при создании службы хранилища Azure, плана службы приложений, функций, веб-приложений и Application Insights.
* [УСТАРЕЛО] Аргумент `--proj-name` не рекомендуется к использованию. Вместо него теперь используется `--proj-file-path`.
* Теперь `az bot publish` удаляет полученные файлы развертывания IIS Node.js, если они уже не существуют.
* В `az bot publish` добавлен аргумент `--keep-node-modules`, чтобы не удалять папку `node_modules` в Службе приложений.
* Добавлена пара "ключ — значение" `"publishCommand"` в выходные данные `az bot create` при создании бота веб-приложения или функции Azure.
  * Значение `"publishCommand"` — это команда `az bot publish` с предварительно заданными обязательными параметрами для публикации созданного бота.
* Обновлено значение `"WEBSITE_NODE_DEFAULT_VERSION"` в шаблоне ARM для ботов SDK версии 4: теперь вместо 8.9.4 указана версия 10.14.1.

### <a name="key-vault"></a>Key Vault
* Исправлена проблема с `keyvault secret backup`, из-за которой некоторые пользователи получали сообщение об ошибке `unexpected_keyword` при использовании `--id`.

### <a name="monitor"></a>Монитор
* Параметр `monitor metrics alert [create|update]` теперь допускает значение измерения `*`.

### <a name="network"></a>Сеть
* Изменено значение `dns zone export` для поддержки экспорта записей CNAME как FQDN.
* В `nic ip-config address-pool [add|remove]` добавлен параметр `--gateway-name` для поддержки серверных пулов адресов шлюза приложений.
* В `network watcher flow-log configure` добавлены аргументы `--traffic-analytics` и `--workspace` для поддержки аналитики трафика через рабочую область Log Analytics.
* В `lb inbound-nat-pool [create|update]` добавлены аргументы `--idle-timeout` и `--floating-ip`.

### <a name="policy-insights"></a>Анализ политик
* Добавлены команды `policy remediation` для поддержки функций исправления политики ресурсов.

### <a name="rdbms"></a>Реляционная СУБД
* Улучшено справочное сообщение и параметры команды.

### <a name="redis"></a>Redis
* Добавлены команды для управления правилами брандмауэра (create, update, delete, show, list).
* Добавлены команды для управления подключением к серверу (create, delete, show, list).
* Добавлены команды для управления расписанием установки исправлений (create, update, delete, show).
* Добавлена поддержка Зон доступности и минимальной версии TLS для операции `redis create.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалены команды `redis update-settings` и `redis list-all`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр для `redis create`: 'tenant settings' не принимается в формате key[=value].
* [УСТАРЕЛО] Добавлено предупреждающее сообщение о том, что команда `redis import-method` больше не поддерживается.

### <a name="role"></a>Роль
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `az identity` перемещена в этот раздел из группы команд `vm`.

### <a name="sql-vm"></a>Виртуальная машина SQL
* [УСТАРЕЛО] Аргумент `--boostrap-acc-pwd` больше не поддерживается из-за опечатки.

### <a name="vm"></a>ВМ
* С параметром `vm list-skus` теперь можно использовать `--all` вместо `--all true`.
* Добавлена команда `vmss run-command [invoke | list | show]`.
* Исправлена ошибка, из-за которой ранее запущенная команда `vmss encryption enable` прекращала работу.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `az identity` перемещена в группу команд `role`.

## <a name="january-31-2019"></a>31 января 2019 г.

Версия 2.0.57

### <a name="core"></a>Основные сведения

* Исправлена [проблема 8399](https://github.com/Azure/azure-cli/issues/8399).

## <a name="january-28-2019"></a>28 января 2019 г.

Версия 2.0.56

### <a name="acr"></a>ACR
* Добавлена поддержка правил виртуальной сети и IP-адресов.

### <a name="acs"></a>ACS
* Добавлена предварительная версия виртуальных узлов.
* Добавлены команды управляемой платформы OpenShift.
* Добавлена поддержка операции обновления субъекта-службы с помощью `aks update-credentials -reset-service-principal`.

### <a name="ams"></a>AMS
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `ams asset get-streaming-locators` переименована в `ams asset list-streaming-locators`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `ams streaming-locator get-content-keys` переименована в `ams streaming-locator list-content-keys`.

### <a name="appservice"></a>Служба приложений
* Добавлена поддержка аналитики приложений для `functionapp create`.
* Добавлена поддержка создания плана службы приложений (включая эластичный план "Премиум") для приложений-функций.
* Исправлены проблемы с настройкой приложений для эластичных планов "Премиум".

### <a name="container"></a>Контейнер
* Добавлена команда `container start`.
* Теперь можно использовать десятичные значения для ЦП при создании контейнеров.

### <a name="eventgrid"></a>Сетка событий
* Добавлен параметр `--deadletter-endpoint` для команды `event-subscription [create|update]`.
* Добавлены новые значения storagequeue и hybridconnection для 'event-subscription [create|update] --endpoint-type`.
* В `event-subscription create` добавлены параметры `--max-delivery-attempts` и `--event-ttl`, чтобы указывать политику повтора для событий.
* В `event-subscription [create|update]` добавлено предупреждающее сообщение, которое отображается, когда в качестве целевого объекта для подписки на события используется веб-перехватчик.
* Добавлен параметр source-resource-id для всех команд, связанных с подпиской на событие, при этом все остальные параметры исходного ресурса помечены как нерекомендуемые.

### <a name="hdinsight"></a>HDInsight
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Из `hdinsight [application] create` удалены параметры `--virtual-network` и `--subnet-name`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]`hdinsight create --storage-account` теперь принимает имя или идентификатор учетной записи хранения вместо конечных точек BLOB-объектов.
* Добавлены параметры `--vnet-name` и `--subnet-name` для `hdinsight create`.
* Для `hdinsight create` добавлена поддержка Корпоративного пакета безопасности и шифрования дисков. 
* Добавлена команда `hdinsight rotate-disk-encryption-key`.
* Добавлена команда `hdinsight update`.

### <a name="iot"></a>Интернет вещей
* Добавлен формат кодирования для команды routing-endpoint.

### <a name="kusto"></a>Kusto
* Предварительный выпуск.

### <a name="monitor"></a>Монитор
* Теперь при сравнении идентификаторов не учитывается регистр.

### <a name="profile"></a>Профиль
* Теперь при операции `login` можно использовать учетную запись уровня клиента для управляемого удостоверения службы.

### <a name="network"></a>Сеть
* Исправлена проблема с `express-route update`, из-за которой игнорировался аргумент `--bandwidth`.
* Исправлена проблема с `ddos-protection update`, из-за которой определение набора вызывало трассировку стека.

### <a name="resource"></a>Ресурс
* Добавлена поддержка файла параметров URI для `group deployment create`.
* Добавлена поддержка управляемого удостоверения для `policy assignment [create|list|show]`.

### <a name="sql-virtual-machine"></a>Виртуальная машина SQL
* Предварительный выпуск.

### <a name="storage"></a>Память
* Теперь исправление обновляет только свойства, измененные в том же объекте.
* Исправлена проблема 8021. Двоичные данные кодируются в кодировке Base64 при возврате.

### <a name="vm"></a>ВМ
* `vm encryption enable` теперь проверяет хранилище ключей для шифрования диска и наличие хранилища ключей для шифрования ключа.
* Добавлен флаг `--force` в `vm encryption enable`.

## <a name="january-15-2019"></a>15 января 2019 г.

Версия 2.0.55

### <a name="acr"></a>ACR
* Теперь можно принудительно отправлять несуществующую диаграмму Helm.
* Разрешены операции среды выполнения без запросов ARM.
* [УСТАРЕЛО] Параметр `--resource-group` больше не поддерживается в следующих командах:
  * `acr login`
  * `acr repository`
  * `acr helm`

### <a name="acs"></a>ACS
* Добавлена поддержка новых регионов ACI.

### <a name="appservice"></a>Служба приложений
* Устранена проблема с отправкой сертификатов для приложений, размещенных в среде службы приложений (ASE) с разными группами ресурсов ASE и приложения.
* Теперь `webapp up` по умолчанию использует SKU P1V1 для Linux.
* Теперь `[webapp|functionapp] deployment source config-zip` отображает правильное сообщение об ошибке при неудачном развертывании. 
* Добавлена команда `webapp ssh`.

### <a name="botservice"></a>Служба Bot
* Добавлены обновления состояния развертывания для `bot create`.

### <a name="configure"></a>Configure
* Добавлен настраиваемый формат выходных данных `none`.

### <a name="cosmosdb"></a>Cosmos DB
* Добавлена поддержка создания базы данных с общей пропускной способностью.

### <a name="hdinsight"></a>HDInsight
* Добавлены команды для управления приложениями.
* Добавлены команды для управления действиями скриптов.
* Добавлены команды для управления Operations Management Suite (OMS).
* Добавлена поддержка регионального использования списка для `hdinsight list-usage`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Из `hdinsight create` удален тип кластера по умолчанию.

### <a name="network"></a>Сеть
* Добавлены аргументы `--custom-headers` и `--status-code-ranges` для команды `traffic-manager profile [create|update]`
* Добавлены новые типы маршрутизации: "Подсеть" и "Многозначный".
* Добавлены аргументы `--custom-headers` и `--subnets` для команды `traffic-manager endpoint [create|update]`  
* Исправлена проблема с возникновением ошибки при указании значения `--vnets ""` для `ddos-protection update`.

### <a name="role"></a>Роль
* [УСТАРЕЛО] Аргумент `--password` для `create-for-rbac` больше не поддерживается. Используйте вместо него надежные пароли, сгенерированные CLI.

### <a name="security"></a>Безопасность
* Начальный выпуск

### <a name="storage"></a>Память
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Количество результатов по умолчанию для `storage [blob|file|container|share] list` теперь 5000. Для возврата всех результатов как ранее используйте `--num-results *`.
* Добавлен параметр `--marker` для команды `storage [blob|file|container|share] list`.
* Добавлена отметка журнала для следующей страницы в STDERR для `storage [blob|file|container|share] list`. 
* Добавлена команда `storage blob service-properties update` с поддержкой статических веб-сайтов.

### <a name="vm"></a>ВМ
* `vm [disk|unmanaged-disk]` и `vmss disk` изменены для лучшего согласования параметров.
* Добавлена поддержка перекрестных ссылок на образы клиента для `[vm|vmss] create`.
* Исправлена ошибка конфигурации по умолчанию в `vm diagnostics get-default-config --windows-os`.
* В `vmss extension set` добавлен аргумент `--provision-after-extensions` для определения расширений, которые необходимо подготовить перед настройкой.
* В `sig image-version update` добавлен аргумент `--replica-count` для определения числа репликаций по умолчанию.
* Исправлена ошибка `image create --source`, из-за которой диск ОС ошибочно принимался за виртуальную машину с тем же именем даже при указании полного идентификатора ресурса.

## <a name="december-20-2018"></a>20 декабря 2018 г.

Версия 2.0.54
### <a name="appservice"></a>Служба приложений
* Исправлена ошибка, когда при повторном развертывании `webapp up` возникала ошибка.
* Добавлена возможность вывода списка моментальных снимков веб-приложений и их восстановления.
* Добавлена поддержка флага `--runtime` в приложениях-функциях Windows.

### <a name="iotcentral"></a>IoT Central
* Исправлен вызов API в команде обновления.

### <a name="role"></a>Роль
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] По умолчанию `ad [app|sp] list` теперь возвращает только первые 100 объектов.

### <a name="sql"></a>SQL
* Добавлена поддержка пользовательских параметров сортировки в управляемых экземплярах.

### <a name="vm"></a>ВМ
* Добавлен параметр `---os-type` для команды `disk create`.

## <a name="december-18-2018"></a>18 декабря 2018 г.

Версия 2.0.53
### <a name="acr"></a>ACR
* Добавлена поддержка импорта изображений из внешних реестров контейнеров.
* Сжатый табличный макет для списка задач.
* Добавлена поддержка URL-адресов Azure DevOps.

### <a name="acs"></a>ACS
* Добавлена предварительная версия виртуальных узлов.
* Из аргументов команды `aks create` удалено "(PREVIEW)".
* [УСТАРЕЛО] Команды `az acs` больше не поддерживаются. Служба ACS будет выведена из эксплуатации 31 января 2020 г.
* Добавлена поддержка политики сети для создания новых кластеров AKS.
* Аргумент `--nodepool-name` команды `aks scale` больше не является обязательным, если есть только один пул узлов.

### <a name="appservice"></a>Служба приложений
* Исправлена проблема, когда команда `webapp config container` не учитывала параметр `--slot`.

### <a name="botservice"></a>Служба Bot
* Добавлена поддержка анализа файла `.bot` при вызове `bot show`.
* Исправлена ошибка подготовки AppInsights.
* Исправлена ошибка с пробелами при работе с путями к файлам.
* Уменьшено количество сетевых вызовов Kudu.
* Улучшен пользовательский интерфейс общих команд.

### <a name="consumption"></a>Потребление
* Исправлены ошибки в API бюджета для отображения уведомлений.

### <a name="cosmosdb"></a>Cosmos DB
* Добавлена возможность преобразования учетной записи из типа "несколько источников" в тип "один источник".

### <a name="maps"></a>Maps
* В `maps account [create|update]` добавлена поддержка SKU S1.

### <a name="network"></a>Сеть
* В команду `watcher flow-log configure` добавлена поддержка аргументов `--format` и `--log-version`.
* Исправлена проблема с командой `dns zone update`, когда использование "" для очистки виртуальных сетей разрешения имен и регистрации не работало.

### <a name="resource"></a>Ресурс
* Исправлена обработка параметра области для групп управления в `policy assignment [create|list|delete|show|update]`. 
* Добавлена новая команда `resource wait`.

### <a name="storage"></a>Память
*  В `storage logging update` добавлена возможность обновления версии схемы журнала для служб хранилища.

### <a name="vm"></a>ВМ
* Исправлено аварийное завершение команды `vm identity remove`, когда указанной виртуальной машине не назначены удостоверения управляемой службы.

## <a name="december-4-2018"></a>4 декабря 2018 г.

Версия 2.0.52
### <a name="core"></a>Основные сведения
* Добавлена поддержка подготовки ресурсов нескольких клиентов для мультитенантного субъекта-службы.
* Исправлена ошибка, когда идентификаторы, передаваемые по конвейеру из команды в формате выходных данных TSV, неправильно анализировались.

### <a name="appservice"></a>Служба приложений
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена команда `webapp up`, которая помогает создавать и развертывать содержимое для приложения.
* Исправлена ошибка в контейнерном приложении Windows из-за изменения в серверной части.

### <a name="network"></a>Сеть
* Добавлен аргумент `--exclusion` в `application-gateway waf-config set` для поддержки исключений WAF.

### <a name="role"></a>Роль
* Добавлена поддержка пользовательских идентификаторов для учетных данных и паролей. 

### <a name="vm"></a>ВМ
* [УСТАРЕЛО] Параметр `vm extension [show|wait] --expand` больше не поддерживается.
* Добавлен параметр`--force` в `vm restart` для повторного развертывания виртуальных машин, которые не отвечают.
* Изменено `[vm|vmss] create --authentication-type` для принятия all и создания виртуальной машины с использованием проверки подлинности на основе пароля и SSH.
* Добавлен параметр `image create --os-disk-caching` для настройки кэширования дисков операционной системы для образа.

## <a name="november-20-2018"></a>20 ноября 2018 г.

Версия 2.0.51
### <a name="core"></a>Основные сведения
* Изменена процедура входа с помощью MSI, чтобы исключить повторное использование имени подписки в удостоверении.

### <a name="acr"></a>ACR
* В шаг задачи добавлен токен контекста.
* Добавлена поддержка для настройки секретов при запуске ACR для зеркалирования задачи ACR.
* Улучшена поддержка параметров `--top` и `--orderby` в командах `show-tags` и `show-manifests`.

### <a name="appservice"></a>Служба приложений
* Для развертываний из ZIP-архивов изменено время ожидания по умолчанию при запросе состояния. Время ожидания увеличено до 5 минут, а также добавлено свойство timeout для настройки этого значения.
* Обновлен номер версии `node_version` по умолчанию. При сбросе операции обмена слотами во время двухэтапного обмена сохраняются все настройки приложения и строки подключения.
* Отменена проверка номера SKU на стороне клиента при создании плана службы приложений для Linux.
* Исправлена ошибка при попытке получения сведений о состоянии для развертывания из ZIP-архива.

### <a name="iotcentral"></a>IotCentral
* Добавлена проверка доступности поддоменов при создании приложения IoT Central.

### <a name="keyvault"></a>Хранилище ключей
* Исправлена проблема, при которой ошибки могли игнорироваться.

### <a name="network"></a>Сеть
* Добавлены подкоманды `root-cert` в `application-gateway` для обработки доверенных корневых сертификатов.
* В команду `application-gateway [create|update]` добавлены параметры `--min-capacity` и `--custom-error-pages`.
* В команду `application-gateway create` добавлен параметр `--zones` для поддержки зоны доступности. 
* В команды `--request-body-check` и `application-gateway waf-config set` добавлены аргументы `--file-upload-limit` и `--max-request-body-size`.

### <a name="rdbms"></a>Rdbms
* Добавлены команды для виртуальной сети MariaDB.

### <a name="rbac"></a>RBAC:
* Исправлена проблема при попытке обновления неизменяемых учетных данных в `ad app update`.
* В выходные данные `ad [app|sp] list` добавлены предупреждения о критических изменениях, ожидаемых в ближайшем будущем. 

### <a name="storage"></a>Память
* Улучшена обработка нетипичных случаев в командах копирования хранилища.
* Исправлена проблема, когда команда `storage blob copy start-batch` не использовала учетные данные для входа при совпадении учетных записей для исходного и целевого объектов.
* Исправлена ошибка в команде `storage [blob|file] url`, когда параметр `sas_token` не включался в URL-адрес.
* В команду `[blob|container] list` добавлено предупреждение о критическом изменении со сведениями о том, что по умолчанию будут выводиться только первые 5000 результатов.

### <a name="vm"></a>ВМ
* В `[vm|vmss] create --storage-sku` добавлена возможность указать номер SKU учетной записи хранения отдельно для управляемых дисков с ОС и данными.
* Изменены параметры для имени версии в `sig image-version`. Теперь используются параметры `--image-version -e`.
* Аргумент `--image-version-name` в команде `sig image-version` отмечен как нерекомендуемый. Он заменен на `--image-version`.
* В `[vm|vmss] create --ephemeral-os-disk` добавлена поддержка для использования локального диска с ОС.
* Добавлена поддержка параметра `--no-wait` в команде `snapshot create/update`.
* Добавлена команда `snapshot wait`.
* Добавлена поддержка для использования имени экземпляра в `[vm|vmss] extension set --extension-instance-name`.

## <a name="november-6-2018"></a>6 ноября 2018 г.

Версия 2.0.50

### <a name="core"></a>Основные сведения
* Добавлена поддержка аутентификации субъекта-службы с помощью имени и издателя сертификата.

### <a name="acr"></a>ACR
* Добавлена поддержка событий git для фиксаций и запросов на вытягивание для исходного триггера задачи.
* Внесено изменение для использования файла Dockerfile по умолчанию, если он не указан в команде build.

### <a name="acs"></a>ACS
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалено `enable_cloud_console_aks_browse`, чтобы активировать az aks browse по умолчанию.

### <a name="advisor"></a>Помощник
* Выпуск общедоступной версии

### <a name="ams"></a>AMS
* Добавлены новые группы команд:
  *  `ams account-filter`
  *  `ams asset-filter`
  *  `ams content-key-policy`
  *  `ams live-event`
  *  `ams live-output`
  *  `ams streaming-endpoint`
  *  `ams mru`
* Добавлены новые команды:
  * `ams account check-name`
  * `ams job update`
  * `ams asset get-encryption-key`
  * `ams asset get-streaming-locators`
  * `ams streaming-locator get-content-keys`
* Добавлена поддержка параметров шифрования в `ams streaming-policy create`.
* Добавлена поддержка операции `ams transform output remove` путем передачи выходного индекса для удаления.
* Добавлены аргументы `--correlation-data` и `--label` в группу команд `ams job`.
* Добавлены аргументы `--storage-account` и `--container` в группу команд `ams asset`.
* Добавлены значения по умолчанию для времени истечения срока действия (23 часа от текущего момента) и разрешений (чтение) в команду `ams asset get-sas-url`. 
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `ams streaming locator` заменена на `ams streaming-locator`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Обновлен аргумент `--content-keys` в `ams streaming locator`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Аргумент `--content-policy-name` команды `ams streaming locator` переименован в `--content-key-policy-name`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `ams streaming policy` заменена на `ams streaming-policy`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Аргумент `--preset-names` в группе команд `ams transform` заменен на `--preset`. Теперь можно одновременно задавать только один вывод/набор параметров (для добавления дополнительных нужно запустить команду `ams transform output add`). Также можно задать пользовательский параметр StandardEncoderPreset, указав путь к пользовательскому файлу JSON.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Аргумент `--output-asset-names ` команды `ams job start` переименован в `--output-assets`. Теперь он принимает список ресурсов, разделенных пробелами, в формате assetName=label. Ресурс без метки можно передать следующим образом: assetName=.

### <a name="appservice"></a>AppService
* Исправлена ошибка в `az webapp config backup update`, которая не давала установить расписание резервного копирования при наличии существующего расписания.

### <a name="configure"></a>Configure
* Добавлен YAML в список поддерживаемых форматов выходных данных.

### <a name="container"></a>Контейнер
* Внесено изменение для показа идентификатора при экспорте группы контейнеров в файл YAML.

### <a name="eventhub"></a>концентратор событий.
* Добавлен флаг `--enable-kafka` для поддержки Kafka в `eventhub namespace [create|update]`.

### <a name="interactive"></a>Интерактивно
* Interactive теперь устанавливает расширение `interactive`, которое обеспечивает более быстрые обновления и поддержку.

### <a name="monitor"></a>Монитор
* Добавлена поддержка имен метрик, которые включают символы прямой косой черты (/) и точки (.), в параметр `--condition` команды `monitor metrics alert [create|update]`.

### <a name="network"></a>Сеть
* Имена команд `network interface-endpoint` не рекомендуются к использованию. Вместо них следует использовать `network private-endpoint`.
* Исправлена ошибка, при которой аргумент `--peer-circuit` в `express-route peering connection create` не принимал идентификатор.
* Исправлена ошибка, приводившая к неправильной работе аргумента `--ip-tags` в команде `public-ip create`. 

### <a name="profile"></a>Профиль
* Добавлен аргумент `--use-cert-sn-issuer` в команду `az login` для входа субъекта-службы с автоматической ротацией сертификатов.

### <a name="rdbms"></a>Реляционная СУБД
* Добавлены команды для работы с репликами MySQL.

### <a name="resource"></a>Ресурс
* Добавлена поддержка групп управления и подписок в команды `policy definition|set-definition`.

### <a name="role"></a>Роль
* Добавлена поддержка управления разрешениями API, входа пользователя, а также управления паролями и сертификатами приложений.
* Изменена команда `ad sp create-for-rbac`, чтобы устранить путаницу между параметром displayName и именем субъекта-службы.
* Добавлена поддержка назначения разрешения для приложений AAD.

### <a name="storage"></a>Память
* Добавлена поддержка подключения к службам хранения с использованием только подписанных URL-адресов и конечных точек (без имени или ключа учетной записи), как описано в `Configure Azure Storage connection strings <https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string>`.

### <a name="vm"></a>ВМ
* Добавлен аргумент `storage-sku` в команду `image create`, позволяющий указать тип учетной записи хранения по умолчанию для образа.
* Исправлена ошибка в команде `vm resize`, из-за которой использование параметра `--no-wait` приводило к аварийному завершению команды.
* Изменен формат выходных данных команды `vm encryption show` в виде таблицы для отображения состояния.
* Изменена команда `vm secret format`, которая теперь требует выходных данных в формате JSON/JSONC. Команда выводит предупреждение и по умолчанию использует для выходных данных формат JSON, если выбран нежелательный формат выходных данных.
* Улучшена проверка аргументов команды `vm create --image`.

## <a name="october-23-2018"></a>23 октября 2018 г.

Версия 2.0.49

### <a name="core"></a>Основные сведения
* Исправлена проблема с аргументом `--ids`, из-за которой аргумент `--subscription` имел приоритет над подпиской, указанной с использованием `--ids`.
* Добавлены явные предупреждения о том, что параметры будут игнорироваться при использовании `--ids`.

### <a name="acr"></a>ACR
* Исправлена ошибка, связанная с шифрованием сборки ACR в Python2.

### <a name="cdn"></a>CDN
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменено стандартное поведение при кешировании строки запроса `cdn endpoint create`. Теперь IgnoreQueryString не является значением по умолчанию. Это значение задает служба.

### <a name="container"></a>Контейнер
* Добавлен тип `Private` в качестве допустимого типа для передачи в --ip-address.
* При настройке подсети для группы контейнеров разрешено использовать только идентификатор подсети.
* Разрешено указывать имя виртуальной сети или идентификатор ресурса для использования виртуальных сетей из разных групп ресурсов.
* Добавлен параметр `--assign-identity`, позволяющий добавить удостоверение MSI для группы контейнеров.
* Добавлен параметр `--scope`, позволяющий создать назначение ролей для удостоверения MSI, назначаемого системой.
* Добавлено предупреждение, которое появляется при создании группы контейнеров с помощью образа без использования длительного процесса.
* Исправлены ошибки, связанные с табличными выходными данными для команд `list` и `show`.

### <a name="cosmosdb"></a>Cosmos DB
* В команду `cosmosdb create` добавлена поддержка параметра `--enable-multiple-write-locations`.

### <a name="interactive"></a>Интерактивно
* Внесены изменения, которые обеспечивают отображение параметра глобальных подписок в списке параметров.

### <a name="iot-central"></a>IoT Central
* Добавлены параметры для шаблона и отображаемого имени, используемые при создании приложения IoT Central.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена поддержка номера SKU F1. Вместо него используйте S1.

### <a name="monitor"></a>Монитор
* Изменения в `monitor activity-log list`:
  * Добавлена поддержка для вывода списка всех событий на уровне подписки.
  * Добавлен параметр `--offset`, чтобы упростить создание запросов времени.
  * Улучшена проверка для `--start-time` и `--end-time`, что позволяет применять широкий диапазон форматов ISO 8601 и более понятные форматы даты и времени.
  * Добавлен параметр `--namespace` в качестве псевдонима для нерекомендуемого параметра `--resource-provider`.
  * Параметр `--filters` отмечен как нерекомендуемый, так как служба не поддерживает значения, отличные от значений со строгой типизацией.
* Изменения в `monitor metrics list`:
  * Добавлен параметр `--offset`, чтобы упростить создание запросов времени.
  * Улучшена проверка для `--start-time` и `--end-time`, что позволяет применять широкий диапазон форматов ISO 8601 и более понятные форматы даты и времени.
* Улучшена проверка аргументов `--event-hub` и `--event-hub-rule` для `monitor diagnostic-settings create`.

### <a name="network"></a>Сеть
* Для `nic create` добавлены аргументы `--app-gateway-address-pools` и `--gateway-name`, которые позволяют добавить внутренний пул адресов Шлюза приложений для сетевого адаптера.
* Для `nic ip-config create/update` добавлены аргументы `--app-gateway-address-pools` и `--gateway-name`, которые позволяют добавить внутренний пул адресов Шлюза приложений для сетевого адаптера.

### <a name="servicebus"></a>Служебная шина
* В класс MigrationConfigProperties добавлено свойство `migration_state` с доступом только для чтения. Это свойство позволяет получить сведения о текущем состоянии миграции с ценовой категории Служебной шины "Стандартный" на ценовую категорию "Премиум".

### <a name="sql"></a>SQL
* Исправлены `sql failover-group create` и `sql failover-group update` для работы с политикой перехода на другой ресурс вручную.

### <a name="storage"></a>Память
* Исправлен формат выходных данных `az storage cors list`: для всех элементов отображается правильный ключ службы.
* Добавлен параметр `--bypass-immutability-policy`, который позволяет удалить контейнер, блокируемый политикой неизменяемости.

### <a name="vm"></a>ВМ
* Для режима кэширования диска принудительно применяется значение `None` при использовании команды `[vm|vmss] create` для виртуальных машин серии Lv или Lv2.
* Для `vm create` обновлен список поддерживаемых размеров для поддерживаемого сетевого ускорителя.
* Для `disk create` добавлены строго типизированные аргументы, которые позволяют настроить скорость операций ввода-вывода и передачи данных в секунду для дисков SSD ценовой категории "Ультра".

## <a name="october-16-2018"></a>16 октября 2018 г.

Версия 2.0.48

### <a name="vm"></a>ВМ
* Исправлена проблема с пакетом SDK, из-за которой установка Homebrew завершалась ошибкой.

## <a name="october-9-2018"></a>9 октября 2018 г.

Версия 2.0.47

### <a name="core"></a>Основные сведения
* Улучшена обработка ошибок типа Bad Request (Недопустимый запрос).

### <a name="acr"></a>ACR
* Добавлена поддержка табличного формата, как в клиенте Helm.

### <a name="acs"></a>ACS
* Добавлен параметр `aks [create|scale] --nodepool-name` для настройки имени пула узлов. Длина имени ограничена 12 символами. Имя по умолчанию — nodepool1. 
* Исправлен возврат к scp при сбое Paramiko.
* Изменена команда `aks create`, которая больше не требует `--aad-tenant-id`.
* Улучшена функция слияния учетных данных Kubernetes при наличии дублированных записей.

### <a name="container"></a>Контейнер
* Изменена команда `functionapp create`, которая теперь поддерживает создание типа для плана потребления Linux с конкретной средой выполнения.
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка для размещения веб-приложений в контейнерах Windows.

### <a name="event-hub"></a>Концентратор событий
* Исправлена команда `eventhub update`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены команды `list` для обработки ошибок NotFound (404) от ресурсов. Теперь ошибки обрабатываются обычным образом вместо отображения пустого списка.

### <a name="extensions"></a>Модули
* Исправлена проблема при попытке добавить расширение, которое уже установлено.

### <a name="hdinsight"></a>HDInsight
* Начальный выпуск

### <a name="iot"></a>Интернет вещей
* На баннер, отображаемый при первом запуске, добавлена команда для установки расширений.

### <a name="keyvault"></a>Хранилище ключей
* Изменены команды для работы с хранилищем ключей.Теперь они ограничены последним профилем API.

### <a name="network"></a>Сеть
* Исправлена команда `network dns zone create`. Теперь команда выполняется успешно, даже если пользователь настроил расположение по умолчанию. См. № 6052.
* `--remote-vnet-id` не рекомендуется к использованию для `network vnet peering create`.
* Добавлена параметр `--remote-vnet` в команду `network vnet peering create`, который принимает имя или идентификатор.
* Добавлена поддержка нескольких префиксов подсетей в команде `network vnet create` с параметром `--subnet-prefixes`.
* Добавлена поддержка нескольких префиксов подсетей в команде `network vnet subnet [create|update]` с параметром `--address-prefixes`.
* Исправлена ошибка в команде `network application-gateway create`, из-за которой было невозможно создать шлюзы с номером SKU `WAF_v2` или `Standard_v2`.
* Добавлен вспомогательный аргумент `--service-endpoint-policy` в команду `network vnet subnet update`.

### <a name="role"></a>Роль
* Добавлена поддержка для списка владельцев приложения Azure AD в команду `ad app owner`.
* Добавлена поддержка для списка владельцев субъекта-службы Azure AD в команду `ad sp owner`.
* Изменены команды для создания и обновления определений ролей, которые теперь принимают несколько конфигураций разрешений.
* Изменена команда `ad sp create-for-rbac`, чтобы универсальный код ресурса (URI) для домашней страницы всегда начинался с https.

### <a name="service-bus"></a>Служебная шина
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены команды `list` для обработки ошибок NotFound (404) от ресурсов. Теперь ошибки обрабатываются обычным образом вместо отображения пустого списка.

### <a name="vm"></a>ВМ
* Исправлено пустое поле `accessSas` в `disk grant-access`.
* Изменена команда `vmss create`, которая теперь резервирует достаточно большой диапазон внешних портов для обработки избыточной подготовки.
* Исправлены команды обновления для `sig`.
* Добавлена поддержка `--no-wait` для управления версиями образов в `sig`.
* Изменена команда `vm list-ip-addresses` для отображения зоны доступности общедоступного IP-адреса.
* Изменена команда `[vm|vmss] disk attach`, которая теперь по умолчанию устанавливает для логического номера диска первое доступно значение.

## <a name="september-21-2018"></a>21 сентября 2018 г.

Версия 2.0.46

### <a name="acr"></a>ACR
* Добавлены команды задач ACR.
* Добавлена команда быстрого запуска.
* Группа команд `build-task` не рекомендуется к использованию.
* Добавлена группа команд `helm` для поддержки управления чартами Helm в ACR.
* Добавлена поддержка создания идемпотентных элементов для управляемого реестра.
* Добавлен флаг отсутствия форматирования для отображения журналов сборки.

### <a name="acs"></a>ACS
* Изменена команда `install-connector` для указания главного полного доменного имени в AKS.
* Исправлена ошибка при назначении ролей для идентификатора подсети виртуальной сети, если не указан субъект-служба и пропущен шаг назначения ролей.

### <a name="appservice"></a>AppService

* Добавлена поддержка операций управления веб-заданиями (как непрерывных, так и активируемых).
* Добавлена поддержка свойства fts-state в az webapp config set. Также добавлена поддержка команд az functionapp config set и az functionapp config show.
* Добавлена возможность использования собственного хранилища для веб-приложений.
* Добавлена возможность вывода списка удаленных веб-приложений и их восстановления.

### <a name="batch"></a>Пакетная служба Azure
* Изменена функция добавления задач с помощью `--json-file` для поддержки синтаксиса AddTaskCollectionParameter.
* Обновлена документация в принятом формате `--json-file`.
* Добавлен параметр `--max-tasks-per-node-option` для команды `batch pool create`.
* Изменен режим работы `batch account` на отображение учетной записи, в которую выполнен вход, если не заданы другие параметры.

### <a name="batch-ai"></a>Batch AI 
* Исправлен сбой при автоматическом создании учетной записи хранения при выполнении команды `batchai cluster create`.

### <a name="cognitive-services"></a>Cognitive Services
* Добавлено средство заполнения для аргументов `--sku`, `--kind` и `--location`.
* Добавлена команда `cognitiveservices account list-usage`.
* Добавлена команда `cognitiveservices account list-kinds`.
* Добавлена команда `cognitiveservices account list`.
* Команда `cognitiveservices list` отмечена как нерекомендуемая.
* Изменен параметр `--name`, который теперь является необязательным для `cognitiveservices account list-skus`.

### <a name="container"></a>Контейнер
* Добавлена возможность перезапуска и остановки выполняющейся группы контейнеров.
* Добавлен параметр `--network-profile` для передачи в сетевой профиль.
* Добавлены параметры `--subnet` и `--vnet_name` для создания групп контейнеров в виртуальной сети.
* Изменены табличные выходные данные для отображения состояния группы контейнеров.

### <a name="datalake"></a>Data Lake
* Добавлены команды для правил виртуальной сети.

### <a name="interactive-shell"></a>Интерактивная оболочка
* Исправлена ошибка в Windows, связанная со сбоем при выполнении команд.
* Исправлена проблема с загрузкой команд в интерактивной оболочке из-за использования нерекомендуемых объектов.

### <a name="iot"></a>Интернет вещей
* Добавлена поддержка маршрутизации Центров Интернета вещей.

### <a name="key-vault"></a>Key Vault
* Исправлена ошибка импорта ключа в Key Vault для ключей RSA.

### <a name="network"></a>Сеть
* Добавлены команды `network public-ip prefix` для поддержки операций с префиксами общедоступных IP-адресов.
* Добавлены команды `network service-endpoint` для поддержки операций с политиками конечной точки службы.
* Добавлены команды `network lb outbound-rule` для поддержки создания правил для исходящего трафика в Load Balancer (цен. категория "Стандартный").
* Добавлен параметр `--public-ip-prefix` для `network lb frontend-ip create/update` для поддержки конфигурации IP внешнего интерфейса с помощью префиксов общедоступных IP-адресов.
* Добавлен параметр `--enable-tcp-reset` для `network lb rule/inbound-nat-rule/inbound-nat-pool create/update`.
* Добавлен параметр `--disable-outbound-snat` для `network lb rule create/update`.
* Добавлена возможность использования `network watcher flow-log show/configure` с классическими группами безопасности сети.
* Добавлена команда `network watcher run-configuration-diagnostic`.
* Исправлена команда `network watcher test-connectivity` и добавлены свойства `--method`, `--valid-status-codes` и `--headers`.
* `network express-route create/update`: добавлен флаг `--allow-global-reach`.
* `network vnet subnet create/update`: добавлена поддержка `--delegation`.
* Добавлена команда `network vnet subnet list-available-delegations`.
* `network traffic-manager profile create/update`: добавлена поддержка `--interval`, `--timeout` и `--max-failures` для конфигурации мониторинга. Не рекомендуются к использованию параметры `--monitor-path`, `--monitor-port` и `--monitor-protocol`, которые следует заменить на `--path`, `--port` и `--protocol`.
* `network lb frontend-ip create/update`: исправлена логика указания метода распределения частных IP-адресов. Если назначается частный IP-адрес, он назначается статически. Если частный IP-адрес не назначается или строка с данными о частных IP-адресах не заполнена, происходит динамическое распределение.
* `dns record-set * create/update`: добавлена поддержка `--target-resource`.
* Добавлены команды `network interface-endpoint` для обращения к объектам конечных точек интерфейса.
* Добавлено `network profile show/list/delete` для частичного управления сетевыми профилями.
* Добавлено `network express-route peering connection` для управления пиринговыми подключениями через ExpressRoute.

### <a name="rdbms"></a>Реляционная СУБД
* Добавлена поддержка MariaDB.

### <a name="reservation"></a>резервирование.
* База данных CosmosDB добавлена в тип перечисления зарезервированных ресурсов.
* Добавлено свойство имени в модели Patch.

### <a name="manage-app"></a>Управление приложением
* Исправлена ошибка в `managedapp create --kind MarketPlace`, приводившая к аварийному завершению создания экземпляра управляемого приложения Marketplace.
* Изменены команды`feature`, действие которых теперь ограничено поддерживаемыми профилями.

### <a name="role"></a>Роль
* Добавлена функция перечисления членства пользователя в группах.

### <a name="signalr"></a>SignalR
* Первый выпуск

### <a name="storage"></a>Память
* Добавлен параметр `--auth-mode login` для использования учетных данных пользователя для авторизации в больших двоичных объектах и очереди.
* Добавлена команда `storage container immutability-policy/legal-hold` для управления неизменяемым хранилищем.

### <a name="vm"></a>ВМ
* Исправлена ошибка, при которой команда `vm create --generate-ssh-keys` перезаписывала файл закрытого ключа, если отсутствовал файл открытого ключа (#4725, #6780).
* Добавлена поддержка общей коллекции изображений с помощью команды `az sig`.

## <a name="august-28-2018"></a>28 августа 2018 г.

Версия 2.0.45

### <a name="core"></a>Основные сведения

* Исправлена проблема с загрузкой пустого файла конфигурации.
* Добавлена поддержка профиля `2018-03-01-hybrid` для Azure Stack.

### <a name="acr"></a>ACR

* Добавлено решение для операций среды выполнения без запросов ARM.
* Внесено изменение для исключения файлов управления версиями (например, файлов с расширениями .git, .gitignore) из отправляемого файла с расширением .tar по умолчанию для команды `build`.

### <a name="acs"></a>ACS

* Внесено изменение в `aks create` с заменой параметрами по умолчанию для виртуальных машин `Standard_DS2_v2`.
* Внесено изменение в `aks get-credentials` для вызова новых API и получения учетных данных кластера.

### <a name="appservice"></a>AppService

* Добавлена поддержка CORS в приложениях-функциях и веб-приложениях.
* Добавлена поддержка тегов ARM для команды создания.
* Внесено изменение в `[webapp|functionapp] identity show` для выхода с кодом 3 при отсутствующем ресурсе.

### <a name="backup"></a>Резервное копирование

* Внесено изменение в `backup vault backup-properties show` для выхода с кодом 3 при отсутствующем ресурсе.

### <a name="bot-service"></a>Служба Bot

* Начальный выпуск CLI для службы Azure Bot

### <a name="cognitive-services"></a>Cognitive Services

* Добавлен новый параметр `--api-properties,`, требуемый для создания некоторых служб.

### <a name="iot"></a>Интернет вещей

* Исправлена проблема со связыванием связанных центров.

### <a name="monitor"></a>Монитор

* Добавлены команды `monitor metrics alert` для создания оповещений метрик почти в реальном времени.
* Команды `monitor alert` не рекомендуются к использованию.

### <a name="network"></a>Сеть

* Внесено изменение в `network application-gateway ssl-policy predefined show` для выхода с кодом 3 при отсутствующем ресурсе.

### <a name="resource"></a>Ресурс

* Внесено изменение в `provider operation show` для выхода с кодом 3 при отсутствующем ресурсе.

### <a name="storage"></a>Память

* Внесено изменение в `storage share policy show` для выхода с кодом 3 при отсутствующем ресурсе.

### <a name="vm"></a>ВМ

* Внесено изменение в `vm/vmss identity show` для выхода с кодом 3 при отсутствующем ресурсе. 
* `--storage-caching` не рекомендуется к использованию для `vm create`.

## <a name="auguest-14-2018"></a>14 августа 2018 г.

Версия 2.0.44

### <a name="core"></a>Основные сведения

* Исправлено отображение чисел в выходных данных `table`.
* Добавлен формат выходных данных YAML.

### <a name="telemetry"></a>Телеметрия

* Улучшены функции отчетности телеметрии.

### <a name="acr"></a>ACR

* Добавлены команды `content-trust policy`.
* Исправлена проблема с правильной обработкой `.dockerignore`.

### <a name="acs"></a>ACS

* Внесено изменение в `az acs/aks install-cli` для установки в разделе `%USERPROFILE%\.azure-kubectl` в Windows.
* Внесено изменение в `az aks install-connector` для определения RBAC в кластере и правильной настройки соединителя ACI.
* Внесено изменение в назначение ролей для подсети в соответствующем случае.
* Внесен новый параметр пропуска назначения ролей для подсети в соответствующем случае.                                 
* Внесено изменение в параметр пропуска назначения ролей для подсети, если назначение уже существует.                

### <a name="appservice"></a>AppService

* Исправлена ошибка с созданием приложения-функции с помощью учетных записей хранения во внешних группах ресурсов.
* Исправлен сбой при развертывании ZIP-архива.

### <a name="batchai"></a>Batch AI

* Внесены изменения в выходные данные средства ведения журнала для автоматического создания учетной записи хранения и определения *группы ресурсов*.        

### <a name="container"></a>Контейнер

* Добавлено `--secure-environment-variables` для передачи переменных среды в контейнер.      

### <a name="iot"></a>Интернет вещей

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалены устаревшие команды, которые были перемещены в расширение Интернета вещей.
* Обновлены элементы для игнорирования домена `azure-devices.net`.

### <a name="iot-central"></a>IoT Central

* Начальный выпуск модуля IoT Central

### <a name="keyvault"></a>Хранилище ключей


* Добавлены команды для управления учетными записями хранения и определений SAS.
* Добавлены команды для правил сети.                                                           
* Добавлен параметр `--id` для операций с секретами, ключами и сертификатами.
* Добавлена поддержка версии с несколькими API управления хранилищем ключей.
* Добавлена поддержка версии с несколькими API плоскости данных хранилища ключей.

### <a name="relay"></a>Ретрансляция

* Начальный выпуск

### <a name="sql"></a>SQL

* Добавлены команды `sql failover-group`.

### <a name="storage"></a>Память

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесено изменение в `storage account show-usage` для запроса параметра `--location` и отображения по регионам.
* Внесено изменение в параметр `--resource-group` для команд `storage account`, который теперь необязателен.
* Удалены предупреждения о нарушении необходимого условия для отдельных сбоев в командах пакетной службы для одного сообщения.
* Внесено изменение в команды `[blob|file] delete-batch`, которые больше не выводят выходной массив значений NULL.
* Внесено изменение в команды `blob [download|upload|delete-batch]`, которые теперь считывают маркер SAS из URL-адреса контейнера.

### <a name="vm"></a>ВМ

* Добавлены общие фильтры для `vm list-skus` для удобства использования.

## <a name="july-31-2018"></a>31 июля 2018 г.

Версия 2.0.43

### <a name="acr"></a>ACR

* В команду `acr build-task show` добавлен флаг `--with-secure-properties`.
* Добавлена команда `acr build-task update-build`.

### <a name="acs"></a>ACS

* При завершении команды `az aks browse` нажатием клавиш CTRL + C теперь возвращается значение 0 (успешное завершение).

### <a name="batch"></a>Пакетная служба Azure

* Исправлена ошибка при отображении маркера AAD в CloudShell.

### <a name="container"></a>Контейнер

* Удалено требование указания `--log-analytics-workspace-key` для имени или идентификатора при выборе подписки.

### <a name="network"></a>Сеть

* В профиль 2017-03-09-profile для Azure Stack добавлена поддержка DNS. 

### <a name="resource"></a>Ресурс

* В `group deployment create` добавлен параметр `--rollback-on-error` для выполнения достоверно корректного развертывания в случае возникновения ошибки.
* Исправлена проблема, из-за которой использование `--parameters {}` с `group deployment create` приводило к ошибке.

### <a name="role"></a>Роль

* Добавлена поддержка профиля 2017-03-09-profile для Azure Stack.
* Исправлена проблема, из-за которой универсальные параметры обновления `app update` работали неправильно.

### <a name="search"></a>Поиск

* Добавлены команды для службы "Поиск Azure".

### <a name="service-bus"></a>Служебная шина

* Добавлена группа команд migration для переноса пространства имен из служебной шины ценовой категории "Стандартный" в служебную шину ценовой категории "Премиум".
* Добавлены новые необязательные свойства для очереди и подписки служебной шины:
  *  `--enable-batched-operations` и `--enable-dead-lettering-on-message-expiration` в `queue`;
  *  `--dead-letter-on-filter-exceptions` в `subscriptions`.

### <a name="storage"></a>Память

* Добавлена поддержка скачивания больших файлов с помощью одного подключения.
* Преобразованы команды `show`, которые ранее отсутствовали: теперь в случае отсутствия ресурса не возвращается код завершения 3.

### <a name="vm"></a>ВМ

* Добавлена поддержка вывода списка групп доступности по подпискам.
* Добавлена поддержка параметра `StandardSSD_LRS`.
* Добавлена поддержка групп безопасности приложений при создании масштабируемого набора виртуальных машин.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены `[vm|vmss] create`, `[vm|vmss] identity assign` и `[vm|vmss] identity remove` для вывода пользовательских удостоверений в формате словаря.

## <a name="july-18-2018"></a>18 июля 2018 г.

Версия 2.0.42

### <a name="core"></a>Основные сведения

* Добавлена поддержка входа в окно WSL bash из браузера.
* Добавлен флаг `--force-string` для всех универсальных команд обновления.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены команды show: сообщения об ошибках записываются в журнал, а команды возвращают код выхода 3, если ресурс отсутствует.

### <a name="acr"></a>ACR

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр --no-push в команде acr build сделан обычным флагом.
* В группу `acr repository` добавлены команды `show` и `update`.
* Добавлен флаг `--detail` для `show-manifests` и `show-tags`, позволяющий выводить более подробные сведения.
* Добавлен параметр `--image`, позволяющий получать сведения о сборке или журналы для определенного образа.

### <a name="acs"></a>ACS

* Поведение `az aks create` изменено так, чтобы выдавалась ошибка, если `--max-pods` меньше 5.

### <a name="appservice"></a>AppService

* Добавлена поддержка номеров SKU для PremiumV2.

### <a name="batch"></a>Пакетная служба Azure

* Исправлена ошибка при использовании учетных данных токена в режиме Cloud Shell.
* Регистр во входных данных JSON теперь не учитывается.

### <a name="batch-ai"></a>Batch AI

* Исправлена команда `az batchai job exec`.

### <a name="container"></a>Контейнер

* Удалено требование указывать имя пользователя и пароль для реестров, не связанных с dockerhub.
* Исправлена ошибка, возникающая при создании групп контейнеров из файла YAML.

### <a name="network"></a>Сеть

* В команду `network nic [create|update|delete]` добавлена поддержка параметра `--no-wait`. 
* Добавлена команда `network nic wait`.
* Аргумент `--ids` команды `network vnet [subnet|peering] list` объявлен устаревшим.
* Добавлен флаг `--include-default`, позволяющий включать в выходные данные команды `network nsg rule list` стандартные правила безопасности.  

### <a name="resource"></a>Ресурс

* В команду `group deployment delete` добавлена поддержка параметра `--no-wait`.
* В команду `deployment delete` добавлена поддержка параметра `--no-wait`.
* Добавлена команда `deployment wait`.
* Исправлена проблема, когда для профиля 2017-03-09-profile по ошибке отображались команды `az deployment` для работы с подписками.

### <a name="sql"></a>SQL

* Исправлена ошибка "The provided resource group name ... did not match the name in the Url" (Указанное имя группы ресурсов ... не соответствовало имени в URL-адресе), которая возникала при указании имени эластичного пула для команд `sql db copy` и `sql db replica create`.
* Разрешена настройка сервера SQL Server по умолчанию с помощью команды `az configure --defaults sql-server=<name>`.
* Реализованы модули форматирования таблиц для команд `sql server`, `sql server firewall-rule`, `sql list-usages` и `sql show-usage`.

### <a name="storage"></a>Память

* В выходные данные команды `storage blob show` добавлено свойство `pageRanges`, которое будет заполняться для страничных BLOB-объектов.

### <a name="vm"></a>ВМ

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] По умолчанию команда `vmss create` теперь использует `Standard_DS1_v2` как размер экземпляра по умолчанию.
* Добавлена поддержка параметра `--no-wait` для `vm extension [set|delete]` и `vmss extension [set|delete]`.
* Добавлена команда `vm extension wait`.

## <a name="july-3-2018"></a>3 июля 2018 г.

Версия 2.0.41

### <a name="aks"></a>AKS

* Теперь для мониторинга используется идентификатор подписки.

## <a name="july-3-2018"></a>3 июля 2018 г.

Версия 2.0.40

### <a name="core"></a>Основные сведения

* Добавлен новый поток кода авторизации для интерактивного входа.

### <a name="acr"></a>ACR

* Добавлено состояние сборки опроса.
* Добавлена поддержка значений перечисления без учета регистра.
* Добавлены параметры `--top` и `--orderby` для `show-manifests`.

### <a name="acs"></a>ACS

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Управление доступом на основе ролей Kubernetes включено по умолчанию.
* Добавлен аргумент `--disable-rbac`. Аргумент `--enable-rbac` не рекомендуется использовать, так как теперь это значение по умолчанию.
* Обновлены параметры команды `aks browse`. Добавлена поддержка параметра `--listen-port`.
* Обновлен пакет диаграмм Helm по умолчанию для команды `aks install-connector`. Используйте файл virtual-kubelet-for-aks-latest.tgz.
* Для обновления существующего кластера добавлены команды `aks enable-addons` и `aks disable-addons`.

### <a name="appservice"></a>AppService

* Добавлена поддержка отключения удостоверения с помощью команды `webapp identity remove`.
* Удален тег `preview` для функции идентификации.

### <a name="backup"></a>Резервное копирование

* Обновлено определение модуля.

### <a name="batchai"></a>Batch AI

* Исправлены табличные выходные данные для команд `batchai cluster node list` и `batchai job node list`.

### <a name="cloud"></a>Cloud

* В облачную конфигурацию добавлен суффикс сервера `acr login`.

### <a name="container"></a>Контейнер

* Для команды `container create` действие по умолчанию изменено на длительную операцию.
* Добавлены параметры Log Analytics `--log-analytics-workspace` и `--log-analytics-workspace-key`.
* Добавлен параметр `--protocol`, с помощью которого можно указать сетевой протокол для использования.

### <a name="extension"></a>Расширение

* Изменена команда `extension list-available`. Теперь с ее помощью отображаются только расширения, совместимые с текущей версией CLI.

### <a name="network"></a>Сеть

* Исправлена проблема с зависимостью типов записей от регистра ([#6602](https://github.com/Azure/azure-cli/issues/6602)).

### <a name="rdbms"></a>Rdbms

* Добавлены команды `[postgres|myql] server vnet-rule`.

### <a name="resource"></a>Ресурс

* Добавлена новая группа операций `deployment`.

### <a name="vm"></a>ВМ

* Добавлена поддержка удаления удостоверения, назначенного системой.

## <a name="june-25-2018"></a>25 июня 2018 г.

Версия 2.0.39

### <a name="cli"></a>CLI

* В установщике MSI обновлена обрезка файлов, чтобы устранить проблему с установкой расширений.

## <a name="june-19-2018"></a>19 июня 2018 г.

Версия 2.0.38

### <a name="core"></a>Основные сведения

* Добавлена глобальная поддержка параметра `--subscription` в большинстве команд.

### <a name="acr"></a>ACR

* Добавлена зависимость `azure-storage-blob`.
* Изменена конфигурация ЦП по умолчанию с `acr build-task create`: теперь используется 2 ядра.

### <a name="acs"></a>ACS

* Обновлены параметры команды `aks use-dev-spaces`. Добавлена поддержка параметра `--update`.
* Изменена команда `aks get-credentials --admin`, чтобы не заменять контекст пользователя в `$HOME/.kube/config`.
* В управляемых кластерах предоставляется доступное только для чтения свойство `nodeResourceGroup`.
* Исправлена ошибка в команде `acs browse`.
* Параметр `--connector-name` стал необязательным для `aks install-connector`, `aks upgrade-connector` и `aks remove-connector`.
* Добавлены новые регионы экземпляров контейнеров Azure для `aks install-connector`.
* В имя выпуска и имя узла Helm добавлено нормализованное расположение для `aks install-connector`.

### <a name="appservice"></a>AppService

* Добавлена поддержка новых версий urllib.
* Добавлена поддержка `functionapp create`, что позволяет использовать план службы приложений из внешних групп ресурсов.

### <a name="batch"></a>Пакетная служба Azure

* Удалена зависимость `azure-batch-extensions`.

### <a name="batch-ai"></a>Batch AI

* Добавлена поддержка рабочих областей. Рабочие области позволяют объединять кластеры, файловые серверы и эксперименты в группы без ограничений по количеству созданных ресурсов.
* Добавлена поддержка экспериментов. Эксперименты позволяют объединять задания в коллекции без ограничений по количеству созданных заданий.
* Добавлена поддержка настройки `/dev/shm` для заданий, выполняющихся в контейнере Docker.
* Добавлены команды `batchai cluster node exec` и `batchai job node exec`. Эти команды позволяют выполнять любые команды непосредственно на узлах и предоставляют функциональные возможности для перенаправления портов.
* Добавлена поддержка параметра `--ids` в командах `batchai`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Все кластеры и файловые серверы необходимо создавать в рабочих областях.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Задания необходимо создавать в рамках экспериментов.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--nfs-resource-group` из команд `cluster create` и `job create`. Для подключения NFS из другой рабочей области или группы ресурсов следует указать идентификатор ARM файлового сервера с помощью параметра `--nfs`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--cluster-resource-group` из команды `job create`. Для отправки задания в кластере, относящемся к другой рабочей области или группе ресурсов, следует указать идентификатор ARM кластера с помощью параметра `--cluster`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален атрибут `location` для заданий, кластера и файловых серверов. Расположение теперь указывается как атрибут рабочей области.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--location` из команд `job create`, `cluster create` и `file-server create`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены имена коротких параметров, чтобы обеспечить согласованность интерфейса:
  - [`--config`, `-c`] переименовано в [`--config-file`, `-f`].
  - [`--cluster`, `-r`] переименовано в [`--cluster`, `-c`].
  - [`--cluster`, `-n`] переименовано в [`--cluster`, `-c`].
  - [`--job`, `-n`] переименовано в [`--job`, `-j`].

### <a name="maps"></a>Maps

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Внесены изменения в `maps account create`. Теперь необходимо принимать условия использования — либо с помощью интерактивной командной строки, либо с помощью флага `--accept-tos`.

### <a name="network"></a>Сеть

* Добавлена поддержка `https` в `network lb probe create` ([№ 6571](https://github.com/Azure/azure-cli/issues/6571)).
* Исправлена проблема, из-за которой в параметре `--endpoint-status` учитывался регистр. [#6502](https://github.com/Azure/azure-cli/issues/6502)

### <a name="reservations"></a>Резервирование

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Добавлен обязательный параметр `ReservedResourceType` для команды `reservations catalog show`.
* Добавлен параметр `Location` для команды `reservations catalog show`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `kind` из команды `ReservationProperties`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр `capabilities` в команде `Catalog` переименован в `sku_properties`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалены свойства `size` и `tier` из команды `Catalog`.
* Добавлен параметр `InstanceFlexibility` для команды `reservations reservation update`.

### <a name="role"></a>Роль

* Улучшена обработка ошибок.

### <a name="sql"></a>SQL

* Исправлена вносившая путаницу ошибка, которая возникала при выполнении команды `az sql db list-editions` для расположения, недоступного для указанной подписки.

### <a name="storage"></a>Память

* Выходные данные таблицы для `storage blob download` изменены на более удобочитаемые.

### <a name="vm"></a>ВМ

* Улучшено уточнение размера виртуальной машины для поддержки ускоренной сети в `vm create`.
* Для `vmss create` добавлено предупреждение о том, что стандартный размер виртуальной машины будет изменен с `Standard_D1_v2` на `Standard_DS1_v2`.
* Добавлен параметр `--force-update` для команды `[vm|vmss] extension set`, что позволяет обновлять расширение, даже если конфигурация не изменялась.

## <a name="june-13-2018"></a>13 июня 2018 г.

Версия 2.0.37

### <a name="core"></a>Основные сведения

* Улучшена интерактивная телеметрия.

## <a name="june-13-2018"></a>13 июня 2018 г.

Версия 2.0.36

### <a name="aks"></a>AKS

* В `aks create` добавлены дополнительные сетевые параметры.
* В `aks create` добавлены аргументы для наблюдения и маршрутизации HTTP-трафика.
* Добавлен аргумент `--no-ssh-key` для команды `aks create`
* Добавлен аргумент `--enable-rbac` для команды `aks create`
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] В `aks create` добавлена поддержка аутентификации Azure Active Directory.

### <a name="appservice"></a>AppService

* Устранена проблема с несовместимыми версиями urllib.

## <a name="june-5-2018"></a>5 июня 2018 г.

Версия 2.0.35

### <a name="interactive"></a>Интерактивно

* Добавлены ограничения в зависимости интерактивного режима.

## <a name="june-5-2018"></a>5 июня 2018 г.

Версия 2.0.34

### <a name="core"></a>Основные сведения

* Добавлена поддержка перекрестных ссылок на ресурсы клиента.
* Улучшена надежность при передаче данных телеметрии.

### <a name="acr"></a>ACR

* Добавлена поддержка VSTS в качестве расположения удаленного источника.
* Добавлена команда `acr import`.

### <a name="aks"></a>AKS

* Изменена команда `aks get-credentials` для создания файла конфигурации kube с более надежными разрешениями файловой системы.

### <a name="batch"></a>Пакетная служба Azure

* Исправлена ошибка, связанная с форматированием таблиц при выполнении команды pool list. [[Ошибка #4378](https://github.com/Azure/azure-cli/issues/4378)]

### <a name="iot"></a>Интернет вещей

* Добавлена возможность создания Центров Интернета вещей категории "Базовый".

### <a name="network"></a>Сеть

* Улучшена команда `network vnet peering`.

### <a name="policy-insights"></a>Анализ политик

* Начальный выпуск

### <a name="arm"></a>ARM

* Добавлены команды `account management-group`.

### <a name="sql"></a>SQL

* Добавлены новые команды для управляемого экземпляра:
  * `sql mi create`
  * `sql mi show`
  * `sql mi list`
  * `sql mi update`
  * `sql mi delete`
* Добавлены новые команды для управляемой базы данных:
  * `sql midb create`
  * `sql midb show`
  * `sql midb list`
  * `sql midb restore`
  * `sql midb delete`

### <a name="storage"></a>Память

* Добавлены типы MIME для JSON и JavaScript, выводимые из расширений файлов.

### <a name="vm"></a>ВМ

* Изменена команда `vm list-skus` для использования фиксированных столбцов, а также добавлено предупреждение об удалении `Tier` и `Size`.
* Добавлен параметр `--accelerated-networking` для команды `vm create`.
* Добавлен параметр `--tags` для команды `identity create`.

## <a name="may-22-2018"></a>22 мая 2018 г.

Версия 2.0.33

### <a name="core"></a>Основные сведения

* Добавлена возможность включения символа `@` в имена файлов.

### <a name="acs"></a>ACS

* Добавлены новые команды для Dev Spaces: `aks use-dev-spaces` и `aks remove-dev-spaces`.
* Исправлена опечатка в справочном сообщении.

### <a name="appservice"></a>AppService

* Улучшены команды общего обновления.
* Добавлена поддержка асинхронного выполнения для `webapp deployment source config-zip`.

### <a name="container"></a>Контейнер

* Добавлена возможность экспорта группы контейнеров в формате YAML.
* Добавлена возможность использования файла YAML для создания или обновления группы контейнеров.

### <a name="extension"></a>Расширение

* Улучшена операция удаления расширений.

### <a name="interactive"></a>Интерактивно

* Изменены параметры ведения журнала для отключения средства синтаксического анализа для завершенных операций.
* Улучшена обработка поврежденных кэшей справки.

### <a name="keyvault"></a>Хранилище ключей

* Исправлены команды хранилища ключей для работы с удостоверениями в Cloud Shell или виртуальных машинах.

### <a name="network"></a>Сеть

* Исправлена проблема, при которой `network watcher show-topology` не будет выполняться, если виртуальной сети или подсети присвоить имя. [#6326](https://github.com/Azure/azure-cli/issues/6326)
* Исправлена проблема, при которой некоторые команды `network watcher` выдают ошибку, что Наблюдатель за сетями не включен в определенных регионах. [#6264](https://github.com/Azure/azure-cli/issues/6264)

### <a name="sql"></a>SQL

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменены объекты ответа, возвращаемые в результатах команд `db` и `dw`:
    * Свойство `serviceLevelObjective` переименовано на `currentServiceObjectiveName`.
    * Удалены свойства `currentServiceObjectiveId` и `requestedServiceObjectiveId`.
    * Тип свойства `maxSizeBytes` изменен со строкового на целое число.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Теперь следующие свойства `db` и `dw` доступны только для чтения:
    * `requestedServiceObjectiveName`.  Для обновления используйте параметр `--service-objective` или задайте свойство `sku.name`.
    * `edition`. Для обновления используйте параметр `--edition` или задайте свойство `sku.tier`.
    * `elasticPoolName`. Для обновления используйте параметр `--elastic-pool` или задайте свойство `elasticPoolId`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Теперь следующие свойства `elastic-pool` доступны только для чтения:
    * `edition`. Для обновления используйте параметр `--edition`.
    * `dtu`. Для обновления используйте параметр `--capacity`.
    *  `databaseDtuMin`. Для обновления используйте параметр `--db-min-capacity`.
    *  `databaseDtuMax`. Для обновления используйте параметр `--db-max-capacity`.
* Добавлены параметры `--family` и `--capacity` для команд `db`, `dw` и `elastic-pool`.
* Добавлены модули форматирования таблиц для команд `db`, `dw` и `elastic-pool`.

### <a name="storage"></a>Память

* Добавлено средство заполнения для аргумента `--account-name`.
* Устранена проблема, связанная с командой `storage entity query`.

### <a name="vm"></a>ВМ

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--write-accelerator` из команды `vm create`. Такие же возможности предоставляет команда `vm update` или `vm disk attach`.
* Устранена проблема с сопоставлением образов расширения в команде `[vm|vmss] extension`.
* Добавлен параметр `--boot-diagnostics-storage` для команды `vm create` для записи журнала загрузки.
* Добавлен параметр `--license-type` для команды `[vm|vmss] update`.

## <a name="may-7-2018"></a>7 мая 2018 г.

Версия 2.0.32

### <a name="core"></a>Основные сведения

* Исправлено необработанное исключение при получении секретов из основной учетной записи службы с сертификатом.
* Добавлена ограниченная поддержка для позиционных аргументов.
* Исправлена проблема, когда `--query` нельзя использовать с `--ids`. [#5591](https://github.com/Azure/azure-cli/issues/5591)
* Улучшены сценарии конвейерной передачи от команд при использовании `--ids`. Добавлена поддержка `-o tsv` с указанием запроса или `-o json` без указания запроса.
* Добавлена команда предложения в случае ошибки, если пользователи вводят команды с опечаткой.
* Исправлена ошибка, когда пользователи вводят `az ''`.
* Добавлена поддержка настраиваемого ресурса для командных модулей и расширений.

### <a name="acr"></a>ACR

* Добавлены команды сборки ACR.
* Исправлена ошибка о не найденных сообщениях об ошибках.
* Оптимизированы производительность создания ресурсов и обработка ошибок.
* Улучшены возможности входа ACR в нестандартные консоли и WSL.
* Улучшены сообщения об ошибках команд репозитория.
* Обновлены столбцы таблиц и порядок их расположения.

### <a name="acs"></a>ACS

* Добавлено предупреждение о том, что `az aks` — это предварительная версия службы.
* Исправлена проблема с разрешением в `aks install-connector`, когда `--aci-resource-group` не указывается.

### <a name="ams"></a>AMS

* Первоначальный выпуск. Управление ресурсами Служб мультимедиа Azure.

### <a name="appservice"></a>Служба приложений

* Исправлена ошибка в `webapp delete`, когда `--slot` предоставляется.
* Удалено `--runtime-version` из `webapp auth update`.
* Добавлена поддержка min\_tls\_version и https2.0.
* Добавлена поддержка нескольких контейнеров.

### <a name="batch-ai"></a>Batch AI

* Изменено `batchai create cluster` с учетом приоритета виртуальной машины при настройке в файле конфигурации кластера.

### <a name="cognitive-services"></a>Cognitive Services

* Исправлена опечатка в примере для `cognitiveservices account create` ([№ 5603](https://github.com/Azure/azure-cli/issues/5603)).

### <a name="consumption"></a>Потребление

* Добавлены новые команды в API для управления бюджетом.

### <a name="container"></a>Контейнер

* Удалено требование `--registry-server` для `container create`, когда сервер реестра включен в имя образа.

### <a name="cosmos-db"></a>Cosmos DB

* Добавлена поддержка VNET для Azure CLI в Cosmos DB

### <a name="dms"></a>DMS

* Исходный выпуск. Добавлена поддержка сценария миграции SQL — Azure SQL.

### <a name="extension"></a>Расширение

* Исправлена ошибка, когда метаданные остановленного расширения отображались.

### <a name="interactive"></a>Интерактивно

* Разрешена работа интерактивных средств завершения с позиционными аргументами.
* Добавлены более понятные выходные данные, когда пользователи вводят \'.
* Исправлены завершения для параметров без справки.
* Исправлены описания для групп команд.

### <a name="lab"></a>Лаборатория

* Исправлены регрессии из преобразования Knack.

### <a name="network"></a>Сеть

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--ids` для:
  * `express-route auth list`
  * `express-route peering list`
  * `nic ip-config list`
  * `nsg rule list`
  * `route-filter rule list`
  * `route-table route list`
  * `traffic-manager endpoint list`

### <a name="profile"></a>Профиль

* Исправлено определение источника `disk create`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалено `--msi-port` и `--identity-port`, так как они больше не используются.
* Исправлена опечатка в кратком описании `account get-access-token`.

### <a name="redis"></a>Redis

* Вместо `redis patch-schedule patch-schedule show` теперь используется `redis patch-schedule show`.
* `redis list-all` теперь не используется. Эта функция включена в `redis list`.
* Вместо `redis import-method` теперь используется `redis import`.
* Добавлена поддержка `--ids` для разных команд.

### <a name="role"></a>Роль

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалено `ad sp reset-credentials` по причине устаревания.

### <a name="storage"></a>Память

* Разрешено применение SAS-токенов назначения к источнику для копирования BLOB-объектов, если SAS источника и ключ учетной записи не указаны.
* Добавлено отображение времени ожидания сокета для отправки и скачивания большого двоичного объекта.
* Добавлена обработка имен больших двоичных объектов, которые начинаются с разделителей пути, как относительных путей.
* Разрешено использовать `storage blob copy --source-sas` с начальным символом запроса "?".
* Исправлено `storage entity query --marker` для принятия списка пар "ключ-значение".

### <a name="vm"></a>ВМ

* Исправлена недопустимая логика обнаружения в URI неуправляемого BLOB-объекта.
* Добавлена поддержка шифрования диска без предоставления пользователем субъектов-служб.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Не используйте ManagedIdentityExtension виртуальной машины для включения поддержки MSI.
* Добавлена поддержка политики вытеснения для `vmss`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалено `--ids` из:
  * `vm extension list`
  * `vm secret list`
  * `vm unmanaged-disk list`
  * `vmss nic list`
* Добавлена поддержка ускорителя записи.
* Добавлена команда `vmss perform-maintenance`.
* Исправлено `vm diagnostics set` для надежного определения типа ОС виртуальной машины.
* Изменено `vm resize` для проверки того, отличается ли запрошенный размер от установленного (с обновлением только при изменении).


## <a name="april-10-2018"></a>10 апреля 2018 г.

Версия 2.0.31

### <a name="acr"></a>ACR

* Улучшена обработка ошибок при откате wincred.

### <a name="acs"></a>ACS

* Срок действия имен субъектов-служб, созданных службой контейнеров Azure, изменен до 5 лет.

### <a name="appservice"></a>Служба приложений

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]: Removed `assign-identity`
* Исправлено неперехватываемое исключение для несуществующих планов веб-приложений.

### <a name="batchai"></a>Batch AI

* Добавлена поддержка API 2018-03-01.

  - Подключение на уровне задания.
  - Переменные среды со значениями секретов.
  - Параметры счетчиков производительности
  - Предоставление информации о сегменте пути конкретного задания.
  - Поддержка вложенных папок в API списка файлов.
  - Предоставление информации об использовании и ограничениях.
  - Возможность указать тип кэширования для NFS-серверов.
  - Поддержка пользовательских образов.
  - Добавлена поддержка инструментария pyTorch.

* Добавлена команда `job wait`, позволяющая дождаться завершения задания и сообщить код выхода задания.
* Добавлена команда `usage show`, позволяющая получить актуальные сведения об использовании и ограничениях ресурсов Batch AI для различных регионов.
* Поддержка национальных облаков.
* Для заданий добавлены аргументы командной строки, которые позволяют подключать файловые системы на уровне заданий. Эти же действия можно выполнять в файлах конфигурации.
* Добавлены дополнительные параметры для настройки кластеров: приоритет виртуальной машины, подсеть, исходное число узлов для кластеров с автоматическим масштабированием, выбор пользовательского образа.
* Добавлен параметр командной строки, который позволяет указать тип кэширования для управляемой файловой системы NFS службы Batch AI.
* Упрощена процедура выбора подключаемой файловой системы в файлах конфигурации. Теперь учетные данные для общего файлового ресурса Azure и контейнеров BLOB-объектов Azure указывать не нужно: CLI получит отсутствующие учетные данные с помощью ключа учетной записи хранения, указанного в параметрах командной строки, или переменной среды либо запросит ключ из службы хранилища Azure (если учетная запись хранения относится к текущей подписке).
* Команда задания потоковой передачи файлов теперь автоматически завершается при завершении задания (успешное выполнение, сбой, прерывание или удаление).
* Улучшены выходные данные `table` для операций `show`.
* Добавлен параметр `--use-auto-storage` для создания кластера. Этот параметр упрощает управление учетными записями хранения, а также подключение общего файлового ресурса Azure и контейнеров BLOB-объектов Azure к кластеру.
* Добавлен параметр `--generate-ssh-keys` для команд `cluster create` и `file-server create`.
* Добавлена возможность запустить задачу настройки узла через командную строку.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команды `job stream-file` и `job list-files` перемещены в группу `job file`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В команде `file-server create` параметр `--admin-user-name` переименован в `--user-name` для согласованности с командой `cluster create`.

### <a name="billing"></a>Выставление счетов

* Добавлены команды для учетной записи регистрации.

### <a name="consumption"></a>Потребление

* Добавлены команды `marketplace`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `reservations summaries` переименована в `reservation summary`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `reservations details` переименована в `reservation detail`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В командах `reservation` удалены короткие параметры `--reservation-order-id` и `--reservation-id`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В командах `reservation summary` удалены короткие параметры `--grain`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] В командах `pricesheet` удалены короткие параметры `--include-meter-details`.

### <a name="container"></a>Контейнер

* Добавлены параметры подключения тома репозитория Git: `--gitrepo-url`, `--gitrepo-dir`, `--gitrepo-revision` и `--gitrepo-mount-path`.
* Исправлена ошибка [#5926](https://github.com/Azure/azure-cli/issues/5926): команда `az container exec` возвращает ошибку, когда указан параметр --container-name.

### <a name="extension"></a>Расширение

* Сообщение о проверке распространения перенесено на уровень отладки.

### <a name="interactive"></a>Интерактивно

* Если команда не распознана, выполнение прерывается.
* Добавлены перехватчики событий, которые используются до и после создания поддерева команд.
* Добавлены сведения о выполнении для параметров `--ids`.

### <a name="network"></a>Сеть

* Исправлена ошибка [#5936](https://github.com/Azure/azure-cli/issues/5936): не задаются теги `application-gateway create`.
* Добавлен аргумент `--auth-certs`, который позволяет подключать сертификаты аутентификации для `application-gateway http-settings [create|update]`. [#4910](https://github.com/Azure/azure-cli/issues/4910).
* Добавлены команды `ddos-protection` для создания планов защиты от атак DDoS.
* В команду `vnet [create|update]` добавлена поддержка `--ddos-protection-plan` для связи виртуальной сети с планом защиты от атак DDoS.
* Исправлена ошибка с флагом `--disable-bgp-route-propagation` в команде `network route-table [create|update]`.
* Удалены фиктивные аргументы `--public-ip-address-type` и `--subnet-type` для команды `network lb [create|update]`.
* В `network dns zone [import|export]` и `network dns record-set txt add-record` добавлена поддержка записей типа TXT с escape-последовательностями RFC 1035.

### <a name="profile"></a>Профиль

* Добавлена поддержка классических учетных записей Azure для команды `account list`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалены аргументы `--msi` & `--msi-port`.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлена команда `georestore`.
* Из команды `create` исключено ограничение на размер хранилища.

### <a name="resource"></a>Ресурс

* Добавлена поддержка параметра `--metadata` в команде `policy definition create`.
* Добавлена поддержка `--metadata`, `--set`, `--add` и `--remove` для команды `policy definition update`.

### <a name="sql"></a>SQL

* Добавлены команды `sql elastic-pool op list` и `sql elastic-pool op cancel`.

### <a name="storage"></a>Память

* Улучшены сообщения об ошибках для строк подключения, имеющих неправильный формат.

### <a name="vm"></a>ВМ

* В команде `vmss create` добавлена возможность настроить для платформы количество доменов сбоя.
* Команда `vmss create` теперь по умолчанию использует стандартную балансировку нагрузки для зональных и больших масштабируемых наборов, а также масштабируемых наборов, в которых отключена одна группа размещения.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]: Removed `vm assign-identity`, `vm remove-identity and `vm format-secret`
* Добавлена поддержка SKU общедоступного IP-адреса для команды `vm create`.
* В команду `vm secret format` добавлены аргументы `--keyvault` и `--resource-group` для тех случаев, когда команда не может разрешить идентификатор хранилища. [#5718](https://github.com/Azure/azure-cli/issues/5718).
* Улучшены сообщения об ошибках для команды `[vm|vmss create]`, когда расположение группы ресурсов не поддерживает зоны.


## <a name="march-27-2018"></a>27 марта 2018 г.

Версия 2.0.30

### <a name="core"></a>Основные сведения

* Отображение сообщения для расширений, которые отмечены в справке как предварительная версия.

### <a name="acs"></a>ACS

* Устранена ошибка с проверкой SSL-сертификата для `aks install-cli` в Cloud Shell.

### <a name="appservice"></a>Служба приложений

* Добавлена поддержка только протокола HTTPS для `webapp update`.
* Добавлена поддержка слотов для `az webapp identity [assign|show]` и `az functionapp identity [assign|show]`.

### <a name="backup"></a>Резервное копирование

* Добавлена новая команда `az backup protection isenabled-for-vm`. Эта команда используется для проверки резервного копирования виртуальной машины в любое хранилище в подписке.
* Включены идентификаторы объектов Azure для параметров `--resource-group` и `--vault-name` для следующих команд:
  * `backup container show`
  * `backup item set-policy`
  * `backup item show`
  * `backup job show`
  * `backup job stop`
  * `backup job wait`
  * `backup policy delete`
  * `backup policy get-default-for-vm`
  * `backup policy list-associated-items`
  * `backup policy set`
  * `backup policy show`
  * `backup protection backup-now`
  * `backup protection disable`
  * `backup protection enable-for-vm`
  * `backup recoverypoint show`
  * `backup restore files mount-rp`
  * `backup restore files unmount-rp`
  * `backup restore restore-disks`
  * `backup vault delete`
  * `backup vault show`
* Изменены параметры `--name` для поддержки формата вывода команд `backup ... show`.

### <a name="container"></a>Контейнер

* Добавлена команда `container exec`. Выполнение команды в контейнере для выполняющейся группы контейнеров.
* Разрешение выходной таблице создавать и обновлять группу контейнеров.

### <a name="extension"></a>Расширение

* Добавлено сообщение для `extension add`, если расширение доступно в режиме предварительной версии.
* Изменен параметр `extension list-available` для отображения всех данных расширения с использованием `--show-details`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменена команда `extension list-available` для отображения упрощенных данных расширения по умолчанию.

### <a name="interactive"></a>Интерактивно

* Изменены операции завершения, активируемые сразу после завершения загрузки таблицы команд.
* Исправлена ошибка с использованием параметра `--style`.
* Отсутствующий интерактивный лексический анализатор создается после дампа таблицы команд.
* Улучшена поддержка средства заполнения.

### <a name="lab"></a>Лаборатория

* Исправлены ошибки с командой `create environment`.

### <a name="monitor"></a>Монитор

* Добавлена поддержка аргументов `--top`, `--orderby` и `--namespace` для `metrics list` ([№ 5785](https://github.com/Azure/azure-cli/issues/5785)).
* Исправлена ошибка [#4529](https://github.com/Azure/azure-cli/issues/5785). Команда `metrics list` принимает разделенный пробелами список метрик для извлечения.
* Добавлена поддержка `--namespace` для `metrics list-definitions` ([№ 5785](https://github.com/Azure/azure-cli/issues/5785)).

### <a name="network"></a>Сеть

* Добавлена поддержка Частных зон DNS.

### <a name="profile"></a>Профиль

* Добавлено предупреждение для `--identity-port` и `--msi-port` в `login`.

### <a name="rdbms"></a>Реляционная СУБД

* Добавлена общедоступная версия 2017-12-01 бизнес-модели API.

### <a name="resource"></a>Ресурс

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]: Changed `provider operation [list|show]` to not require `--api-version`

### <a name="role"></a>Роль

* Добавлена поддержка конфигурации требуемого уровня доступа и собственных клиентов для `az ad app create`.
* Изменены команды `rbac`, чтобы возвращать не более 1000 идентификаторов в разрешении объекта.
* Добавлены команды `ad sp credential [reset|list|delete]` для управления учетными данными.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр properties из выходных данных `az role assignment [list|show]`.
* Добавлена поддержка разрешений `dataActions` и `notDataActions` для `role definition`.

### <a name="storage"></a>Память

* Исправлена проблема с отправкой файла размером от 195 до 200 ГБ.
* Исправлена ошибка [#4049](https://github.com/Azure/azure-cli/issues/4049). Проблемы игнорирования параметров условия при отправке добавочного большого двоичного объекта.

### <a name="vm"></a>ВМ

* Добавлено предупреждение `vmss create` для предстоящих критически важных изменений для наборов из 100 и более экземпляров.
* Добавлена поддержка устойчивости зон для `vm [snapshot|image]`.
* Изменено представление экземпляра диска для улучшения отчета о состоянии шифрования.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.]`vm extension delete` Изменена команда, которая больше не возвращает выходные данные.

## <a name="march-13-2018"></a>13 марта 2018 г.

Версия 2.0.29

### <a name="acr"></a>ACR

* Добавлена поддержка параметра `--image` для `repository delete`.
* Параметры `--manifest` и `--tag` команды `repository delete` отмечены как нерекомендуемые.
* Добавлена команда `repository untag` для удаления тега без удаления данных.

### <a name="acs"></a>ACS

* Добавлена команда `aks upgrade-connector` для обновления существующего соединителя.
* Изменены файлы конфигурации `kubectl`. Теперь используется более разборчивый стиль YAML с разбивкой на блоки.

### <a name="advisor"></a>Помощник

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `advisor configuration get` переименована в `advisor configuration list`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `advisor configuration set` переименована в `advisor configuration update`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена команда `advisor recommendation generate`.
* Добавлен параметр `--refresh` для команды `advisor recommendation list`.
* Добавлена команда `advisor recommendation show`.

### <a name="appservice"></a>Служба приложений

* Команда `[webapp|functionapp] assign-identity` отмечена как нерекомендуемая.
* Добавлены команды управляемого удостоверения `webapp identity [assign|show]` и `functionapp identity [assign|show]`.

### <a name="eventhubs"></a>Концентраторы событий

* Начальный выпуск

### <a name="extension"></a>Расширение

* Добавлена проверка, позволяющая предупредить пользователя, если используемый дистрибутив отличается от хранящегося в исходном файле пакета, так как это может привести к возникновению ошибок.

### <a name="interactive"></a>Интерактивно

* Исправлена ошибка [#5625](https://github.com/Azure/azure-cli/issues/5625). Теперь записи журнала сохраняются в разных сеансах.
* Исправлена ошибка [#3016](https://github.com/Azure/azure-cli/issues/3016). При использовании области не создавались записи журнала.
* Исправлена ошибка [#5688](https://github.com/Azure/azure-cli/issues/5688). Если при загрузке таблицы команд возникало исключение, варианты автозаполнения не отображались.
* Исправлен индикатор хода выполнения для длительных операций.

### <a name="monitor"></a>Монитор

* Команды `monitor autoscale-settings` отмечены как нерекомендуемые.
* Добавлены команды `monitor autoscale`.
* Добавлены команды `monitor autoscale profile`.
* Добавлены команды `monitor autoscale rule`.

### <a name="network"></a>Сеть

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удален параметр `--tags` из `route-filter rule create`.
* Удалены некоторые ошибочные значения по умолчанию для следующих команд:
  * `network express-route update`
  * `network nsg rule update`
  * `network public-ip update`
  * `traffic-manager profile update`
  * `network vnet-gateway update`
* Добавлены команды `network watcher connection-monitor`.
* Добавлены параметры `--vnet` и `--subnet` для `network watcher show-topology`.

### <a name="profile"></a>Профиль

* Параметр `--msi` для `az login` отмечен как нерекомендуемый.
* Добавлен параметр `--identity` для `az login`. Он заменяет `--msi`.

### <a name="rdbms"></a>Реляционная СУБД

* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Внесены изменения для использования предварительной версии API 2017-12-01.

### <a name="service-bus"></a>Служебная шина

* Начальный выпуск

### <a name="storage"></a>Память

* Исправлена ошибка [#4971](https://github.com/Azure/azure-cli/issues/4971): теперь `storage blob copy` поддерживает другие облака Azure.
* Исправлена ошибка [#5286](https://github.com/Azure/azure-cli/issues/5286). При пакетном выполнении команд `storage blob [delete-batch|download-batch|upload-batch]` больше не отображается сообщение об ошибке в предусловии.

### <a name="vm"></a>ВМ

* В `[vm|vmss] create` добавлена поддержка присоединения неуправляемых дисков данных и настройки кэширования.
* `[vm|vmss] assign-identity` и `[vm|vmss] remove-identity` отмечены как нерекомендуемые.
* Вместо нерекомендуемых команд добавлены команды `vm identity [assign|remove|show]` и `vmss identity [assign|remove|show]`.
* Для приоритета `vmss create` по умолчанию установлено значение None.

## <a name="february-27-2018"></a>27 февраля 2018 г

Версия 2.0.28

### <a name="core"></a>Основные сведения

* Исправлена ошибка [#5184](https://github.com/Azure/azure-cli/issues/5184). Проблема с установкой Homebrew.
* Добавлена поддержка телеметрии расширения с применением пользовательских ключей.
* Добавлена функция ведения журнала HTTP в `--debug`.

### <a name="acs"></a>ACS

* Изменено для использования диаграмм Helm `virtual-kubelet-for-aks` для `aks install-connector` по умолчанию.
* Исправлена ошибка с недостаточными разрешениями субъектов-служб на создание групп контейнеров ACI.
* Добавлены параметры `--aci-container-group`, `--location` и `--image-tag` для `aks install-connector`.
* Удалено уведомление об устаревании из `aks get-versions`.

### <a name="appservice"></a>Служба приложений

* Обновления для новой версии пакета SDK (azure-mgmt-web 0.35.0)
* Исправлена ошибка [#5538](https://github.com/Azure/azure-cli/issues/5538): `Free` указывалось как недопустимый номер SKU.

### <a name="cognitive-services"></a>Cognitive Services

* Обновлено уведомление при создании новой учетной записи Cognitive Services.

### <a name="consumption"></a>Потребление

* Добавлены новые команды для резервирования API прейскуранта.
* Обновлены существующие форматы сведений об использовании и резервировании.

### <a name="container"></a>Контейнер

* Добавлены аргументы `--secrets` и `--secrets-mount-path` в командах `container create` для использования секретов в ACI.

### <a name="network"></a>Сеть

* Исправлена ошибка [#5559](https://github.com/Azure/azure-cli/issues/5559). Отсутствующий клиент в `network vnet-gateway vpn-client generate`.

### <a name="resource"></a>Ресурс

* Изменено `group deployment export` для отображения частичного шаблона и ошибок при сбое.

### <a name="role"></a>Роль

* Добавлено `role assignment list-changelogs` для включения аудита ролей субъекта-службы.

### <a name="sql"></a>SQL

* Добавлена поддержка избыточности зон для создания и обновления баз данных и эластичных пулов.

### <a name="storage"></a>Память

* Включено определение пути назначения и префикса для `storage blob [upload-batch|download-batch]`.

### <a name="vm"></a>ВМ

* Добавлена поддержка присоединения и отсоединения дисков на одном экземпляре VMSS.


## <a name="february-13-2018"></a>13 февраля 2018 г.

Версия 2.0.27

### <a name="core"></a>Основные сведения

* Изменен способ аутентификации при входе с помощью MSI: применяется ключ при использовании идентификатора подписки и имени.

### <a name="acs"></a>ACS

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Переименовано `aks get-versions` на `aks get-upgrades` для точности.
* Изменено `aks get-versions` для отображения версий Kubernetes, доступных для `aks create`.
* Изменены значения по умолчанию `aks create`, чтобы разрешить серверу выбирать версию Kubernetes.
* Обновлены справочные сообщения, связанные с субъектом-службой и создаваемые AKS.
* Изменены стандартные размеры узла для `aks create` с уровня Standard\_D1\_v2 на уровень Standard\_DS1\_v2.
* Улучшена надежность при поиске панели мониторинга для группы pod для `az aks browse`.
* Исправлена команда `aks get-credentials` для обработки ошибок Юникода при загрузке файлов конфигурации Kubernetes.
* Добавлено сообщение в `az aks install-cli`, чтобы помочь получить `kubectl` в `$PATH`.

### <a name="appservice"></a>Служба приложений

* Исправлена проблема со сбоем `webapp [backup|restore]` из-за пустой ссылки.
* Добавлена поддержка стандартных планов службы приложений с помощью `az configure --defaults appserviceplan=my-asp`.

### <a name="cdn"></a>CDN

* Добавлены команды `cdn custom-domain [enable-https|disable-https]`.

### <a name="container"></a>Контейнер

* Добавлен параметр `--follow` для `az container logs` для журналов потоковой передачи.
* Добавлена команда `container attach`, которая добавляет локальный стандартный поток вывода и поток вывода сообщений об ошибках к контейнеру в группе контейнеров.

### <a name="cosmosdb"></a>Cosmos DB

* Добавлена поддержка настройки параметров.

### <a name="extension"></a>Расширение

* Добавлена поддержка параметра `--pip-proxy` для команд `az extension [add|update]`.
* Добавлена поддержка аргумента `--pip-extra-index-urls` для команд `az extension [add|update]`.

### <a name="feedback"></a>Отзывы

* Добавлены сведения о расширении к данным телеметрии.

### <a name="interactive"></a>Интерактивно

* Исправлена проблема, когда пользователю предлагалось войти в интерактивном режиме в Cloud Shell.
* Исправлена регрессия с отсутствующей функцией заполнения параметров.

### <a name="iot"></a>Интернет вещей

* Исправлена проблема, когда `iot dps access policy [create|update]` в случае успешного выполнения возвращает сообщение об ошибке "Не найдено".
* Исправлена проблема, когда `iot dps linked-hub [create|update]` в случае успешного выполнения возвращает сообщение об ошибке "Не найдено".
* Добавлена поддержка параметра `--no-wait` для `iot dps access policy [create|update]` и `iot dps linked-hub [create|update]`.
* Изменена команда `iot hub create` для указания числа секций.

### <a name="monitor"></a>Монитор

* Исправлена команда `az monitor log-profiles create`.

### <a name="network"></a>Сеть

* Исправлен параметр `--tags` для следующих команд:
  * `network public-ip create`
  * `network lb create`
  * `network local-gateway create`
  * `network nic create`
  * `network vnet-gateway create`
  * `network vpn-connection create`

### <a name="profile"></a>Профиль

* Включена команда `az login` для использования в интерактивном режиме.

### <a name="resource"></a>Ресурс

* Снова добавлена команда `feature show`.

### <a name="role"></a>Роль

* Добавлен аргумент `--available-to-other-tenants` для команды `ad app update`

### <a name="sql"></a>SQL

* Добавлены команды `sql server dns-alias`.
* Добавлена команда `sql db rename`.
* Добавлена поддержка аргумента `--ids` для команд SQL.

### <a name="storage"></a>Память

* Добавлены команды `storage blob service-properties delete-policy` и `storage blob undelete` для включения обратимого удаления.

### <a name="vm"></a>ВМ

* Исправлена ошибка, когда шифрование виртуальной машины инициализировалось не полностью.
* Добавлены выходные данные идентификатора субъекта для включения MSI.
* Фиксированное значение `vm boot-diagnostics get-boot-log`


## <a name="january-31-2018"></a>31 января 2018 г.

Версия 2.0.26

### <a name="core"></a>Основные сведения

* Добавлена поддержка получения необработанного маркера в контексте MSI.
* Удалена строка индикатора опроса после завершения LRO при выполнении cmd.exe в Windows.
* Добавлено предупреждение, которое появляется при использовании настроенных по умолчанию параметров, измененных на запись уровня INFO. Используйте `--verbose` для просмотра.
* Добавьте индикатор хода выполнения для ожидания команд.

### <a name="acs"></a>ACS

* Добавлено описание аргумента `--disable-browser`.
* Улучшено заполнение нажатием клавиши TAB для аргументов `--vm-size`.

### <a name="appservice"></a>Служба приложений

* Фиксированное значение `webapp log [tail|download]`
* Удалена проверка `kind` в веб-приложениях и функциях.

### <a name="cdn"></a>CDN

* Устранена проблема с отсутствием клиента для команды `cdn custom-domain create`.

### <a name="cosmosdb"></a>Cosmos DB

* Исправлено описание параметров для политик отработки отказа.

### <a name="interactive"></a>Интерактивно

* Исправлена проблема, когда заполнение параметров команд больше не выполнялось.

### <a name="network"></a>Сеть

* Добавлена защита `--cert-password` для `application-gateway create`.
* Исправлена проблема с `application-gateway update`, когда команда `--sku` ошибочно применяла значение по умолчанию.
* Добавлена защита `--shared-key` и `--authorization-key` для `vpn-connection create`.
* Устранена проблема с отсутствием клиента для команды `asg create`.
* Добавлен параметр `--file-name / -f` для экспортируемых имен в `dns zone export`.
* Исправлены следующие ошибки для `dns zone export`:
  * Исправлена проблема, когда длинные записи ТХТ неправильно экспортировались.
  * Исправлена проблема, когда записи ТХТ в кавычках неправильно экспортировались без символов экранирования.
* Исправлена проблема, когда некоторые записи импортировались дважды с помощью `dns zone import`.
* Восстановлены команды `vnet-gateway root-cert` и `vnet-gateway revoked-cert`.

### <a name="profile"></a>Профиль

* Исправлена команда `get-access-token` для работы в виртуальной машине с идентификатором.

### <a name="resource"></a>Ресурс

* Исправлена ошибка с `deployment [create|validate]`, когда предупреждение неправильно отображалось, если поле type шаблона содержало значения в верхнем регистре.

### <a name="storage"></a>Память

* Исправлена проблема с переносом учетных записей хранения из версии 1 в версию 2.
* Добавлены отчеты о ходе выполнения всех команд отправки или скачивания.
* Исправлена ошибка, которая препятствовала использованию параметра аргумента -n с `storage account check-name`.
* Добавлен столбец snapshot в табличные выходные данные для `blob [list|show]`.
* Исправлены ошибки с разными параметрами, которые должны были анализироваться как целые числа.

### <a name="vm"></a>ВМ

* Добавлена команда `vm image accept-terms` для разрешения создания виртуальных машин из образов с дополнительными расходами.
* Исправлена команда `[vm|vmss create]` для выполнения команд с прокси-сервера с помощью неподписанных сертификатов.
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка режима низкого приоритета для VMSS.
* Добавлена защита `--admin-password` для `[vm|vmss] create`.


## <a name="january-17-2018"></a>17 января 2018 г.

Версия 2.0.25

### <a name="acr"></a>ACR

* Добавлена возможность отката входа ACR при ошибках учетных данных в Windows.
* Включены журналы реестра.

### <a name="acs"></a>ACS

* Исправлена команда `get-credentials`.
* Удалено требование роли для имени субъекта-службы.

### <a name="appservice"></a>Служба приложений

* Исправлена ошибка с `config ssl upload`, при которой значение `hosting_environment_profile` было NULL.
* В `browse` добавлена поддержка для пользовательских URL-адресов.
* Исправлена поддержка слотов для `log tail`.

### <a name="backup"></a>Резервное копирование

* Параметр `--container-name` для `backup item list` теперь не является обязательным.
* В `backup restore restore-disks` добавлены варианты учетной записи хранения.
* Для проверки расположения в `backup protection enable-for-vm` добавлена чувствительность к регистру.
* Устранена проблема, при которой команды завершались сбоем с указанием недопустимого имени контейнера.
* В `backup item list` теперь по умолчанию включен параметр Health Status.

### <a name="batch"></a>Пакетная служба Azure

* `batch login` теперь возвращает сведения об аутентификации.

### <a name="cloud"></a>Cloud

* При установке `--profile` в облаке теперь не требуется указывать конечные точки.

### <a name="consumption"></a>Потребление

* Добавлены новые команды для резервирования: `consumption reservations summaries` и `consumption reservations details`.

### <a name="event-grid"></a>Сетка событий Azure

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команды `az eventgrid topic event-subscription` перемещены в `eventgrid event-subscription`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команды `az eventgrid resource event-subscription` перемещены в `eventgrid event-subscription`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Удалена команда `eventgrid event-subscription show-endpoint-url`. Вместо нее следует использовать `eventgrid event-subscription show --include-full-endpoint-url`.
* Добавлена команда `eventgrid topic update`.
* Добавлена команда `eventgrid event-subscription update`.
* Добавлен параметр `--ids` для команд `eventgrid topic`.
* Добавлена поддержка заполнения нажатием клавиши TAB для имен разделов.

### <a name="interactive"></a>Интерактивно

* Устранена проблема, при которой интерактивный режим не работал с Python 2.x.
* Исправлены ошибки при запуске.
* Устранена проблема, при которой некоторые команды не работали в интерактивном режиме.

### <a name="iot"></a>Интернет вещей

* Добавлена поддержка службы подготовки устройств.
* В команды и справочную информацию о них добавлены сообщения о нерекомендуемых версиях.
* Теперь при проверке Интернета вещей указывается расширение Интернета вещей.

### <a name="monitor"></a>Монитор

* Добавлена поддержка параметра нескольких диагностических операций. Теперь параметр `--name` является обязательным для `az monitor diagnostic-settings create`.
* Добавлена команда `monitor diagnostic-settings categories` для получения категории параметров диагностики.

### <a name="network"></a>Сеть

* Устранена проблема с `vnet-gateway update` при попытке входа в активный режим и режим ожидания или выхода из них.
* Для `application-gateway [create|update]` добавлена поддержка HTTP2.

### <a name="profile"></a>Профиль

* Добавлена поддержка для входа с использованием назначенных пользователям удостоверений.

### <a name="role"></a>Роль

* В `role assignment create` добавлен аргумент `--assignee-object-id`, чтобы обойти запрос графов.

### <a name="service-fabric"></a>Service Fabric

* В результат проверки при создании кластера добавлены подробные сведения об ошибках.
* Устранена проблема отсутствия клиента при выполнении некоторых команд.

### <a name="vm"></a>ВМ

* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Поддержка разных зон для `vmss`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Значение по умолчанию `vmss` для одной зоны заменено данными подсистемы балансировки нагрузки уровня "Стандартный".
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Для EMSI параметр `externalIdentities` изменен на `userAssignedIdentities`.
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка переключения дисков ОС.
* Добавлена поддержка использования образов виртуальных машин из других подписок.
* В `[vm|vmss] create` добавлены аргументы `--plan-name`, `--plan-product`, `--plan-promotion-code` и `--plan-publisher`.
* Устранены проблемы с `[vm|vmss] create`.
* Устранена проблема чрезмерного использования ресурсов из-за `vm image list --all`.

## <a name="december-19-2017"></a>19 декабря 2017 г.

Версия 2.0.23

* Добавлена поддержка для входа с использованием назначенных пользователям удостоверений.

### <a name="container"></a>Контейнер

* Исправлен неправильный порядок параметров для журналов контейнеров.

### <a name="network"></a>Сеть

* Добавлен аргумент `--disable-bgp-route-propagation` для команды `route-table [create|update]`
* Добавлен аргумент `--ip-tags` для команды `public-ip [create|update]`

### <a name="storage"></a>Память

* Добавлена поддержка хранилища версии 2.

### <a name="vm"></a>ВМ

* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка для назначенных пользователям удостоверений для виртуальных машин и VMSS.


## <a name="december-5-2017"></a>5 декабря 2017 г.

Версия 2.0.22

* Удалена команда `az component`. Вместо нее следует использовать `az extension`.

### <a name="core"></a>Основные сведения
* Конечная точка `AZURE_US_GOV_CLOUD` центра AAD изменена с login.microsoftonline.com на login.microsoftonline.us.
* Исправлена проблема с непрерывной отправкой телеметрии.

### <a name="acs"></a>ACS

* Добавлены команды `aks install-connector` и `aks remove-connector`.
* Улучшены сообщения об ошибках для команды `acs create`.
* Добавлена возможность использования команды `aks get-credentials -f` без полного пути.

### <a name="advisor"></a>Помощник

* Начальный выпуск

### <a name="appservice"></a>Служба приложений

* Добавлена возможность создания имени сертификата с помощью команды `webapp config ssl upload`.
* Исправлены команды `webapp [list|show]` и `functionapp [list|show]` для отображения правильных приложений.
* Добавлено стандартное значение для переменной `WEBSITE_NODE_DEFAULT_VERSION`.

### <a name="consumption"></a>Потребление

* Добавлена поддержка API версии 2017-11-30.

### <a name="container"></a>Контейнер

* Исправлены стандартные порты регрессии.

### <a name="monitor"></a>Монитор

* Добавлена поддержка нескольких измерений для команд метрик.

### <a name="resource"></a>Ресурс

* Добавлен аргумент `--include-response-body` для команды `resource show`

### <a name="role"></a>Роль

* Добавлено отображение стандартных назначений "классических" администраторов для команды `role assignment list`.
* Добавлена поддержка команды `ad sp reset-credentials` для добавления учетных данных вместо перезаписи.
* Улучшены сообщения об ошибках для команды `ad sp create-for-rbac`.

### <a name="sql"></a>SQL

* Добавлены команды `sql db list-usages` и `sql db show-usage`.
* Добавлены команды `sql server conn-policy show` и `sql server conn-policy update`.

### <a name="vm"></a>ВМ

* Добавлены сведения о зонах для команды `az vm list-skus`.


## <a name="november-14-2017"></a>14 ноября 2017 г.

Версия 2.0.21

### <a name="acr"></a>ACR

* Добавлена поддержка создания веб-перехватчиков в регионах репликации.


### <a name="acs"></a>ACS

* В AKS агент теперь называется узлом.
* Параметр `--orchestrator-release` для командлета `acs create` не рекомендуется использовать.
* Изменен размер виртуальной машины для AKS по умолчанию на `Standard_D1_v2`.
* Исправлена команда `az aks browse` для Windows.
* Исправлена команда `az aks get-credentials` для Windows.

### <a name="appservice"></a>Служба приложений

* Добавлен источник развертывания `config-zip` для веб-приложений и приложений-функций.
* Добавлен параметр `--docker-container-logging` для команды `az webapp log config`.
* Удален параметр `storage` из параметра `--web-server-logging` для команды `az webapp log config`.
* Улучшены сообщения об ошибках для команды `deployment user set`.
* Добавлена поддержка создания приложений-функций Linux
* Фиксированное значение `list-locations`

### <a name="batch"></a>Пакетная служба Azure

* Исправлена ошибка в команде создания пула, когда идентификатор ресурса использовался с флагом `--image`.

### <a name="batchai"></a>Модуль искусственного интеллекта пакетной службы

* Добавлен короткий параметр `-s` для параметра `--vm-size` при указании размера виртуальной машины в команде `file-server create`.
* Добавлены аргументы ключа и имени учетной записи хранения в параметры команды `cluster create`.
* Исправлена документация по командам `job list-files` и `job stream-file`.
* Добавлен короткий параметр `-r` для параметра `--cluster-name` при указании имени кластера в команде `job create`.

### <a name="cloud"></a>Cloud

* Изменена команда `cloud [register|update]` для предотвращения регистрации облаков, для которых отсутствуют необходимые конечные точки.

### <a name="container"></a>Контейнер

* Добавлена поддержка открытия нескольких портов.
* Добавлена политика перезапуска группы контейнеров.
* Добавлена поддержка подключения файлового ресурса Azure в качестве тома.
* Обновлена вспомогательная документация.

### <a name="data-lake-analytics"></a>Data Lake Analytics

* Изменена команда `[job|account] list` для возврата более кратких сведений.

### <a name="data-lake-store"></a>Data Lake Storage

* Изменена команда `account list` для возврата более кратких сведений.

### <a name="extension"></a>Расширение

* Добавлена команда `extension list-available` для отображения официальных расширений Майкрософт.
* Добавлен параметр `--name` для команды `extension [add|update]` для установки расширений по имени.

### <a name="iot"></a>Интернет вещей

* Добавлена поддержка центров сертификации (ЦС) и цепочек сертификатов.

### <a name="monitor"></a>Монитор

* Добавлены команды `activity-log alert`.

### <a name="network"></a>Сеть

* Добавлена поддержка DNS-записей типа CAA.
* Исправлена проблема, когда конечные точки могли не обновляться с помощью команды `traffic-manager profile update`.
* Исправлена проблема, когда команда `vnet update --dns-servers` не работала в зависимости от способа создания виртуальной сети.
* Исправлена проблема, когда относительные DNS-имена неправильно импортировались с помощью команды `dns zone import`.

### <a name="reservations"></a>Резервирование

* Первоначальный выпуск предварительной версии

### <a name="resource"></a>Ресурс

* Добавлена поддержка идентификаторов ресурсов для блокировок на уровне ресурсов и параметра `--resource`.

### <a name="sql"></a>SQL

* Добавлен параметр `--ignore-missing-vnet-service-endpoint` для команды `sql server vnet-rule [create|update]`.

### <a name="storage"></a>Память

* Изменена команда `storage account create` для использования номера SKU `Standard_RAGRS` по умолчанию.
* Исправлены ошибки при обработке имени файла или большого двоичного объекта, содержащего символы, отличные от ASCII.
* Исправлена ошибка, препятствующая использованию параметра `--source-uri` с командой `storage [blob|file] copy start-batch`.
* Добавлены команды для удаления нескольких объектов с помощью команды `storage [blob|file] delete-batch`.
* Исправлена проблема с включением метрик с помощью команды `storage metrics update`.
* Исправлена проблема с файлами более 200 ГБ при использовании команды `storage blob upload-batch`.
* Исправлена проблема, когда параметры `--bypass` и `--default-action` игнорировались командой `storage account [create|update]`.

### <a name="vm"></a>ВМ

* Исправлена ошибка с командой `vmss create`, препятствующая использованию уровня `Basic`.
* Добавлены аргументы `--plan` для команды `[vm|vmss] create` для пользовательских образов с информацией о выставлении счетов.
* Добавлены команды `vm secret `[add|remove|list]`.
* Переименование `vm format-secret` в `vm secret format`
* Добавлен аргумент `--encrypt format` для команды `vm encryption enable`

## <a name="october-24-2017"></a>24 октября 2017 г.

Версия 2.0.20

### <a name="core"></a>Основные сведения

* Обновление `2017-03-09-profile` для использования API `MGMT_STORAGE` версии `2016-01-01`

### <a name="acr"></a>ACR

* Обновление службы управления ресурсами для указания API версии `2017-10-01`
* Изменение номера SKU BYOS-хранилища на "Классический"
* Переименование номеров SKU реестра на "Базовый", "Стандартный" и "Премиум"

### <a name="acs"></a>ACS

* [ПРЕДВАРИЕТЛЬНАЯ ВЕРСИЯ] Добавление команд `az aks`
* Исправление Kubernetes `get-credentials`

### <a name="appservice"></a>Служба приложений

* Исправление проблемы с недопустимыми скачанными журналами `webapp`

### <a name="component"></a>Компонент

* Добавление более понятного сообщения об устаревании для всех установщиков, а также запроса на подтверждение

### <a name="monitor"></a>Монитор

* Добавлены команды `action-group`.

### <a name="resource"></a>Ресурс

* Исправление несовместимости с самой последней версии зависимости msrest в `group export`
* Исправление `policy assignment create` для работы с определениями встроенных политик и наборов политик

### <a name="vm"></a>ВМ

* Добавлен аргумент `--accelerated-networking` для команды `vmss create`


## <a name="october-9-2017"></a>9 октября 2017 г.

Версия 2.0.19

### <a name="core"></a>Основные сведения

* В Azure Stack добавлена обработка URL-адресов центра ADFS с завершающей косой чертой.

### <a name="appservice"></a>Служба приложений

* Добавлена возможность общего обновления с помощью новой команды `webapp update`.

### <a name="batch"></a>Пакетная служба Azure

* Обновление до пакета SDK для пакетной службы версии 4.0.0
* Обновлен параметр `--image` для команды VirtualMachineConfiguration, обеспечивающий поддержку ссылок на образы ARM в дополнение к publish:offer:sku:version.
* Добавлена поддержка новой модели расширений CLI для команд расширений пакетной службы.
* Удалена поддержка пакетной службы для модели компонентов.

### <a name="batchai"></a>Модуль искусственного интеллекта пакетной службы

* Исходный выпуск модуля искусственного интеллекта пакетной службы

### <a name="keyvault"></a>Хранилище ключей

* Исправлена проблема с аутентификацией Key Vault при использовании ADFS в Azure Stack. [(#4448)](https://github.com/Azure/azure-cli/issues/4448)

### <a name="network"></a>Сеть

* Аргумент `--server` для `application-gateway address-pool create` изменен на необязательный (разрешено использование пустых пулов адресов).
* Обновлен элемент `traffic-manager` для поддержки последних функций.

### <a name="resource"></a>Ресурс

* Добавлена поддержка параметров `--resource-group/-g` для изменения имени группы ресурсов на `group`.
* Добавлены команды для `account lock` для работы с блокировками на уровне подписки.
* Добавлены команды для `group lock` для работы с блокировками на уровне группы.
* Добавлены команды для `resource lock` для работы с блокировками на уровне ресурса.

### <a name="sql"></a>SQL

* Добавлена поддержка SQL TDE и BYOK TDE.
* Добавлена команда `db list-deleted` и параметр `db restore --deleted-time` для поиска и восстановления удаленных баз данных.
* Добавлено `db op list` и `db op cancel` для отображения и отмены выполняющихся операций в базе данных.

### <a name="storage"></a>Память

* Добавлена поддержка моментальных снимков файловых ресурсов.

### <a name="vm"></a>Виртуальные машины

* Исправлена ошибка в команде `vm show`, когда использование `-d` вызывало сбой отсутствующих частных IP-адресов.
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка последовательного обновления для команды `vmss create`.
* Добавлена поддержка обновления параметров шифрования с помощью команды `vm encryption enable`.
* Добавлен параметр `--os-disk-size-gb` для команды `vm create`.
* Добавлен параметр `--license-type` для команды `vmss create` (для Windows).


## <a name="september-22-2017"></a>22 сентября 2017 г.

Версия 2.0.18

### <a name="resource"></a>Ресурс

* Добавлена поддержка отображения определений встроенных политик
* Добавлена поддержка параметра mode для создания определения политик
* Добавлена поддержка шаблонов и определений пользовательского интерфейса для команды `managedapp definition create`
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Изменен тип ресурса `managedapp` с `appliances` на `applications` и с `applianceDefinitions` на `applicationDefinitions`.

### <a name="network"></a>Сеть

* Добавлена поддержка зоны доступности для подкоманд `network lb` и `network public-ip`
* Добавлена поддержка IPv6 (пиринг Майкрософт) для `express-route`
* Добавлены команды группы безопасности приложения `asg`
* Добавлен аргумент `--application-security-groups` для команды `nic [create|ip-config create|ip-config update]`
* Добавлены аргументы `--source-asgs` и `--destination-asgs` для команды `nsg rule [create|update]`
* Добавлены аргументы `--ddos-protection` и `--vm-protection` для команды `vnet [create|update]`
* Добавлены команды `network [vnet-gateway|vpn-client|show-url]`.

### <a name="storage"></a>Память

* Исправлена проблема, когда команды `storage account network-rule` могут не работать после обновления пакета SDK

### <a name="eventgrid"></a>Сетка событий

* Обновлен пакет SDK Python для службы "Сетка событий Azure" для использования новой версии API 2017-09-15-preview

### <a name="sql"></a>SQL

* Аргумент `--resource-group` для команды `sql server list` сделан необязательным. Если он не указан, возвращаются все серверы SQL Server в подписке
* Добавлен параметр `--no-wait` для команд `db [create|copy|restore|update|replica create|create|update]` и `dw [create|update]`

### <a name="keyvault"></a>Хранилище ключей

* Добавлена поддержка команд хранилища ключей за прокси-сервером

### <a name="vm"></a>ВМ

* Добавлена поддержка зоны доступности для команды `[vm|vmss|disk] create`
* Исправлена проблема, когда использование аргумента `--app-gateway ID` с командой `vmss create` приводило к сбою
* Добавлен аргумент `--asgs` для команды `vm create`
* Добавлена поддержка выполнения команд на виртуальных машинах с помощью команды `vm run-command`
* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлена поддержка шифрования диска VMSS с помощью команды `vmss encryption`
* Добавлена поддержка обслуживания виртуальных машин с помощью команды `vm perform-maintenance`

### <a name="acs"></a>ACS

* [ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ] Добавлен аргумент `--orchestrator-release` для команды `acs create` для регионов служб ACS (предварительная версия)

### <a name="appservice"></a>Служба приложений

* Добавлена возможность обновлять и отображать параметры аутентификации с помощью команды `webapp auth [update|show]`

### <a name="backup"></a>Резервное копирование

* Предварительный выпуск.


## <a name="september-11-2017"></a>11 сентября 2017 г.

Версия 2.0.17

### <a name="core"></a>Основные сведения

* Включен командный модуль для настройки собственного идентификатора корреляции в телеметрии.
* Исправлена проблема с дампом JSON, когда для телеметрии настроен режим диагностики.

### <a name="acs"></a>ACS

* Добавлена команда `acs list-locations`.
* Для `ssh-key-file` предоставляется ожидаемое значение по умолчанию.

### <a name="appservice"></a>Служба приложений

* Добавлена возможность создавать веб-приложения в группе ресурсов в рамках плана службы, отличной от активной.

### <a name="cdn"></a>CDN

* Исправлена ошибка "CustomDomain не пригоден к итерации" для `cdn custom-domain create`.

### <a name="extension"></a>Расширение

* Начальный выпуск

### <a name="keyvault"></a>Хранилище ключей

* Исправлена проблема с зависимостью разрешений от регистра для `keyvault set-policy`.

### <a name="network"></a>Сеть

* Переименование `vnet list-private-access-services` в `vnet list-endpoint-services`
* Аргумент `--private-access-services` переименован в `--service-endpoints` для команды `vnet subnet create/update`.
* Добавлена поддержка нескольких диапазонов IP-адресов и портов в командах `nsg rule create/update`.
* Добавлена поддержка номера SKU в команде `lb create`.
* Добавлена поддержка номера SKU в команде `public-ip create`.

### <a name="resource"></a>Ресурс

* Разрешена передача в определениях параметров политики ресурсов в командах `policy definition create` и `policy definition update`.
* Разрешена передача значений параметров для команды `policy assignment create`.
* Разрешена передача JSON или файла для всех параметров.
* Версия API изменена на более позднюю.

### <a name="sql"></a>SQL

* Добавлены команды `sql server vnet-rule`.

### <a name="vm"></a>ВМ

* Исправлено: Не назначать доступ, если `--scope` не предоставляется.
* Исправлено: Использовать те же расширения имен, что и для портала.
* Удалено `subscription` из выходных данных `[vm|vmss] create`.
* Исправлено: номер SKU хранилища `[vm|vmss] create` неприменим для дисков данных с образом.
* Исправлено: `vm format-secret --secrets` не принимает идентификаторы, разделенные строками.

## <a name="august-31-2017"></a>31 августа 2017 г.

Версия 2.0.16

### <a name="keyvault"></a>Хранилище ключей

* Исправлена ошибка при попытке автоматически разрешить шифрование секрета с помощью `secret download`.

### <a name="sf"></a>Sf

* Объявлены неподдерживаемыми все команды; вместо них используется Service Fabric CLI (sfctl).

### <a name="storage"></a>Память

* Исправлена проблема, когда учетные записи хранения нельзя было создавать в регионах, которые не поддерживают функцию NetworkACLs.
* Определение типа и кодировки содержимого во время передачи больших двоичных объектов и файла, если оба свойства не указаны.

## <a name="august-28-2017"></a>28 августа 2017 г.

Версия 2.0.15

### <a name="cli"></a>CLI

* Для `--version` добавлено юридическое примечание.

### <a name="acs"></a>ACS

* Исправлены регионы, в которых доступна предварительная версия.
* Правильно отформатирован параметр по умолчанию `dns_name_prefix`.
* Оптимизированы выходные данные команды ACS.

### <a name="appservice"></a>Служба приложений

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Исправлены несоответствия в выходных данных `az webapp config appsettings [delete|set]`.
* Добавлен новый псевдоним `-i` в команду `az webapp config container set --docker-custom-image-name`.
* Предоставлен параметр `az webapp log show`.
* Предоставлены новые аргументы команды `az webapp delete`, которые позволяют сохранить план службы приложений, метрики и данные регистрации DNS.
* Исправлено: Правильно определять параметры слота.

### <a name="iot"></a>Интернет вещей

* Исправлена ошибка #3934. При создании политики существующие политики больше не удаляются.

### <a name="network"></a>Сеть

* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Команда `vnet list-private-access-services` переименована в `vnet list-endpoint-services`.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр `--private-access-services` переименован в `--service-endpoints` в командах `vnet subnet [create|update]`.
* Добавлена поддержка нескольких диапазонов IP-адресов и портов в командах `nsg rule [create|update]`.
* Добавлена поддержка номера SKU в команде `lb create`.
* Добавлена поддержка номера SKU в команде `public-ip create`.

### <a name="profile"></a>Профиль

* Предоставлены параметры `--msi` и `--msi-port` для входа с использованием удостоверения виртуальной машины.

### <a name="service-fabric"></a>Service Fabric

* Предварительный выпуск.
* Упрощены правила реестра для паролей и имен пользователя для команды.
* Исправлена строка для ввода пароля пользователем даже после передачи параметра.
* Добавлена поддержка пустого значения `registry_cred`.

### <a name="storage"></a>Память

* Появилась возможность задавать уровень большого двоичного объекта.
* Добавлены аргументы `--bypass` и `--default-action` в командах `storage account [create|update]` для поддержки туннелирования службы.
* Добавлены команды для добавления правил виртуальной сети и правил на основе IP-адресов в `storage account network-rule`.
* Разрешено шифрование службы с управляемым пользователем ключом.
* [КРИТИЧЕСКИ ВАЖНОЕ ИЗМЕНЕНИЕ.] Параметр `--encryption` переименован в `--encryption-services` в команде `az storage account create and az storage account update`.
* Исправление 4220. Несоответствие синтаксиса `az storage account update encryption`.

### <a name="vm"></a>ВМ

* Исправлена проблема, из-за которой для команды `vmss get-instance-view` отображались лишние и неправильные сведения при использовании параметра `--instance-id *`.
* Добавлена поддержка параметра `--lb-sku` в команде `vmss create`.
* Из черного списка имен администраторов для команд `[vm|vmss] create` удалены имена людей.
* Исправлена проблема, из-за которой команда `[vm|vmss] create` выдавала ошибку, если из образа не удавалось извлечь сведения о плане.
* Исправлена проблема, которая приводила к сбою при создании масштабируемого набора виртуальных машин с внутренней подсистемой балансировки нагрузки.
* Исправлена проблема, из-за которой аргумент `--no-wait` не работал с командой `vm availability-set create`.


## <a name="august-15-2017"></a>15 августа 2017 г.

Версия 2.0.14

### <a name="acs"></a>ACS

* Исправлен номер порта sshMaster0 для Kubernetes.

### <a name="appservice"></a>Служба приложений

* Исправлено исключение при создании веб-приложения Linux на основе Git.

### <a name="event-grid"></a>Сетка событий Azure

* Добавлены зависимости пакета SDK.

## <a name="august-11-2017"></a>11 августа 2017 г.

Версия 2.0.13

### <a name="acs"></a>ACS

* Добавлены дополнительные регионы, в которых доступна предварительная версия.

### <a name="batch"></a>Пакетная служба Azure

* Пакет SDK для пакетной службы обновлен до версии 3.1.0, а пакет SDK для управления пакетной службой — до версии 4.1.0.
* Добавлена новая команда для отображения количества задач в задании.
* Исправлена ошибка при обработке URL-адреса SAS файла ресурсов.
* Для конечной точки учетной записи пакетной службы теперь поддерживается дополнительный префикс https://.
* Поддерживается добавление к заданию списков, содержащих более 100 задач.
* Добавлено ведение журнала отладки при загрузке командного модуля расширений.

### <a name="component"></a>Компонент

* Добавлено предупреждение о прекращении поддержки в команды az component.

### <a name="container"></a>Контейнер

* `create`: исправлена проблема, из-за которой запрещалось использовать знак равенства в переменной среды.


### <a name="data-lake-store"></a>Data Lake Storage

* Включен индикатор хода выполнения.

### <a name="event-grid"></a>Сетка событий Azure

* Начальный выпуск

### <a name="network"></a>Сеть

* `lb`: исправлена проблема, из-за которой некоторые имена дочерних ресурсов не разрешались правильно, если не были указаны.
* `application-gateway {subresource} delete`: исправлена проблема, из-за которой не учитывался параметр `--no-wait`.
* `application-gateway http-settings update`: исправлена проблема, из-за которой не удавалось отключить параметр `--connection-draining-timeout`.
* Исправлена проблема с непредвиденным аргументом ключевого слова `sa_data_size_kilobyes` при использовании с командой `az network vpn-connection ipsec-policy add`.

### <a name="profile"></a>Профиль

* `account list`: добавлен параметр `--refresh` для синхронизации последних подписок с сервером.

### <a name="storage"></a>Память

* Включено обновление учетной записи хранения с помощью удостоверения, назначенного системой.

### <a name="vm"></a>ВМ

* `availability-set`: предоставлено число доменов сбоя при преобразовании.
* Предоставлена команда `list-skus`.
* Поддерживается назначение удостоверений без создания назначений ролей.
* При подключении дисков данных применяется номер SKU хранилища.
* Удалено используемое по умолчанию имя диска ОС и номер SKU хранилища при использовании управляемых дисков.


## <a name="july-28-2017"></a>28 июля 2017 г.

Версия 2.0.12

* Добавлены команды для контейнеров.
* Добавлены модули выставления счетов и потребления ресурсов.

```text
azure-cli (2.0.12)

acr (2.0.9)
acs (2.0.11)
appservice (0.1.11)
batch (3.0.3)
billing (0.1.3)
cdn (0.0.6)
cloud (2.0.7)
cognitiveservices (0.1.6)
command-modules-nspkg (2.0.1)
component (2.0.6)
configure (2.0.10)
consumption (0.1.3)
container (0.1.7)
core (2.0.12)
cosmosdb (0.1.11)
dla (0.0.10)
dls (0.0.11)
feedback (2.0.6)
find (0.2.6)
interactive (0.3.7)
iot (0.1.10)
keyvault (2.0.8)
lab (0.0.9)
monitor (0.0.8)
network (2.0.11)
nspkg (3.0.1)
profile (2.0.9)
rdbms (0.0.5)
redis (0.2.7)
resource (2.0.11)
role (2.0.9)
sf (1.0.5)
sql (2.0.8)
storage (2.0.11)
vm (2.0.11)
```

### <a name="core"></a>Основные сведения

* Вывод сведений о пакетах SDK для проверки подлинности субъектов-служб с помощью сертификатов.
* Исправлены исключения в ходе выполнения развертывания.
* Для создания клиента подписки используется конечная точка ARM текущего облака.
* Улучшена параллельная обработка файла clouds.config (3636).
* Обновление идентификатора клиентского запроса при каждом выполнении команды.
* Создание клиентов подписки с правильным профилем SDK (3635).
* Создание отчетов о ходе выполнения развертывания шаблонов (3510).
* Добавлена поддержка выбора полей вывода в таблице с помощью запроса jmespath (3581).
* Улучшено отключение аргументов анализа и добавлено ведение журналов с помощью жестов (3434).
* Создание клиентов подписки с правильным профилем SDK.
* Перемещение всех существующих файлов записи в последнюю папку.
* Исправлена идемпотентность при создании виртуальной машины или масштабируемого набора виртуальных машин (3586).
* В путях команд больше не учитывается регистр.
* В определенных параметрах логического типа больше не учитывается регистр.
* Поддержка входа на локальный сервер AD FS, например Azure Stack.
* Исправлена параллельная запись в файл clouds.config (3255).

### <a name="acr"></a>ACR

* Добавлена команда `show-usage` для управляемых реестров.
* Поддержка обновления номера SKU для управляемых реестров.
* Добавлены управляемые реестры с управляемыми номерами SKU.
* Добавлены веб-перехватчики для управляемых реестров с использованием модуля для команды acr webhook.
* Добавлена проверка подлинности с помощью AAD с использованием команды acr login.
* Добавлена команда delete для репозиториев, манифестов и тегов Docker.

### <a name="acs"></a>ACS

* Поддержка API версии 2017-07-01.

### <a name="appservice"></a>Служба приложений

* Исправлена ошибка, из-за которой команда вывода списка в веб-приложениях Linux не возвращала данные.
* Поддержка извлечения учетных данных из ACR.
* Удаление всех команд группы `appservice web`.
* Создание масок паролей для реестров Docker из выходных данных команды (3656).
* Обеспечено использование браузера по умолчанию в macOS без ошибок (3623).
* Улучшена справка по командам `webapp log tail` и `webapp log download` (3624).
* Предоставлена команда `traffic-routing` для настройки статической маршрутизации (3566).
* Добавлены исправления, связанные с надежностью, при настройке системы управления версиями (3245).
* Удален неподдерживаемый аргумент `--node-version` из функции `webapp config update` для веб-приложений Windows. Вместо него используется `webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=...`.

### <a name="batch"></a>Пакетная служба Azure

* Пакеты SDK для пакетной службы обновлены до версии 3.0.0 с поддержкой низкоприоритетных виртуальных машин в пулах.
* Параметр команды `pool create` переименован с `--target-dedicated` в `--target-dedicated-nodes`.
* Для команды `pool create` добавлены параметры `--target-low-priority-nodes` и `--application-licenses`.

### <a name="cdn"></a>CDN

* Предоставлено улучшенное сообщение об ошибке для команды `cdn endpoint list`, которое отображается, если заданный параметром `--profile-name` профиль не существует.

### <a name="cloud"></a>Cloud

* Формат версии API конечной точки метаданных облака изменен на следующий: ГГГГ-ММ-ДД.
* Конечная точка коллекции не является обязательной.
* Поддерживается регистрация облака только с конечной точкой диспетчера ARM.
* Предоставлен параметр в команде `cloud set` для выбора профиля при выборе текущего облака.
* Предоставлен параметр `endpoint_vm_image_alias_doc`.

### <a name="cosmosdb"></a>Cosmos DB

* Внесены исправления, которые позволяют создавать коллекцию с пользовательским ключом секции.
* Добавлена поддержка срока жизни по умолчанию для коллекции.

### <a name="data-lake-analytics"></a>Data Lake Analytics

* Добавлены команды для управления политикой вычислений в разделе `dla account compute-policy`.
* Добавлена команда `dla job pipeline show`.
* Добавлена команда `dla job recurrence list`.

### <a name="data-lake-store"></a>Data Lake Storage

* Добавлена поддержка смены ключей пользовательского хранилища ключей в `dls account update`.
* Обновлена версия пакета SDK для базовой файловой системы Data Lake Store из-за проблем с производительностью.
* Добавлена команда `dls enable-key-vault`. Эта команда пытается активировать пользовательское хранилище ключей, предназначенное для шифрования данных в учетной записи Data Lake Store.

### <a name="interactive"></a>Интерактивно

* Улучшено время запуска с помощью кэшированных команд.
* Увеличено тестовое покрытие.
* Расширены возможности жеста "?", которые позволяют также вставить его в следующую команду.
* Исправлены ошибки с интерактивными функциями в предварительной версии профиля 2017-03-09 (3587).
* Разрешено использовать `--version` в качестве параметра в интерактивном режиме (3645).
* В интерактивном режиме больше не возникают ошибки при выполнении проверки (3570).
* Создание отчетов о ходе выполнения развертывания шаблонов (3510).
* Добавлен флаг `--progress`.
* Из вариантов завершения удалены `--debug` и `--verbose`.
* Из вариантов завершения удален `interactive` (3324).

### <a name="iot"></a>Интернет вещей

* Исправлено. При создании политики больше не удаляются существующие политики (3934).

### <a name="key-vault"></a>Хранилище ключей

* Добавлены команды для функций восстановления хранилища ключей:
  * `keyvault`, подкоманды `purge`, `recover` и `keyvault list-deleted`.
  * `keyvault secret`, подкоманды `backup`, `restore`, `purge`, `recover` и `list-deleted`;
  * `keyvault certificate`, подкоманды `purge`, `recover` и `list-deleted`.
  * `keyvault key`, подкоманды `purge`, `recover` и `list-deleted`.
* Добавлена интеграция хранилища ключей с субъектом-службой (3133).
* Обновлена плоскость данных хранилища ключей до версии 0.3.2 (3307).

### <a name="lab"></a>Лаборатория

* Добавлена поддержка запросов ко всем виртуальным машинам в лаборатории с помощью `az lab vm claim`.
* Добавлен модуль форматирования табличных выходных данных команд `az lab vm list` и `az lab vm show`.

### <a name="monitor"></a>Монитор

* Исправлены ошибки с файлом шаблона с помощью команды `monitor autoscale-settings get-parameters-template` (3349).
* Переименование `monitor alert-rule-incidents list` в `monitor alert list-incidents`
* Переименование `monitor alert-rule-incidents show` в `monitor alert show-incident`
* Переименование `monitor metric-defintions list` в `monitor metrics list-definitions`
* Переименование `monitor alert-rules` в `monitor alert`
* В команду `monitor alert create` внесены следующие изменения:
  * подкоманды `condition` и `action` больше не принимают данные JSON;
  * добавлены различные параметры, которые упрощают процесс создания правила;
  * параметр `location` больше не требуется;
  * добавлена поддержка имени и идентификатора для целевого объекта;
  * удален параметр `--alert-rule-resource-name`;
  * параметр `is-enabled` переименован в `enabled`, он больше не требуется;
  * значения по умолчанию для `description` теперь основаны на указанном условии;
  *  добавлены примеры, в которых подробно представлен новый формат.
* Поддержка имен или идентификаторов для команд `monitor metric`.
* Добавлены вспомогательные аргументы и примеры использования команды `monitor alert rule update`.

### <a name="network"></a>Сеть

* Добавлена команда `list-private-access-services`.
* Добавлен аргумент `--private-access-services` в команды `vnet subnet create` и `vnet subnet update`.
* Исправлена проблема, из-за которой команда `application-gateway redirect-config create` завершалась ошибкой.
* Исправлена проблема, из-за которой команда `application-gateway redirect-config update` не работала с параметром `--no-wait`.
* Исправлена ошибка при использовании аргумента `--servers` с командами `application-gateway address-pool create` и `application-gateway address-pool update`.
* Добавлены команды `application-gateway redirect-config`.
* В команду `application-gateway ssl-policy` добавлены подкоманды `list-options`, `predefined list` и `predefined show`.
* В команду `application-gateway ssl-policy set` добавлены аргументы `--name`, `--cipher-suites` и `--min-protocol-version`.
* В команды `application-gateway http-settings create` и `application-gateway http-settings update` добавлены аргументы `--host-name-from-backend-pool`, `--affinity-cookie-name`, `--enable-probe` и `--path`.
* В команды `application-gateway url-path-map create` и `application-gateway url-path-map update` добавлены аргументы `--default-redirect-config` и `--redirect-config`.
* Добавлен аргумент `--redirect-config` в команду `application-gateway url-path-map rule create`.
* Добавлена поддержка параметра `--no-wait` в команде `application-gateway url-path-map rule delete`.
* В команды `application-gateway probe create` и `application-gateway probe update` добавлены аргументы `--host-name-from-http-settings`, `--min-servers`, `--match-body` и `--match-status-codes`.
* Добавлен аргумент `--redirect-config` в команды `application-gateway rule create` и `application-gateway rule update`.
* Добавлена поддержка аргумента `--accelerated-networking` в командах `nic create` и `nic update`.
* Удален аргумент `--internal-dns-name-suffix` из команды `nic create`.
* Добавлена поддержка аргумента `--dns-servers` в командах `nic update` и `nic create`. Добавлена поддержка аргумента --dns-servers.
* Исправлена ошибка, из-за которой команда `local-gateway create` игнорировала параметр `--local-address-prefixes`.
* Добавлена поддержка параметра `--dns-servers` в команде `vnet update`.
* Исправлена ошибка при создании пиринга без фильтрации маршрутов с помощью команды `express-route peering create`.
* Исправлена ошибка, из-за которой аргументы `--provider` и `--bandwidth` не работали с командой `express-route update`.
* Исправлена ошибка с логикой установки значений по умолчанию в команде `network watcher show-topology`.
* Улучшено форматирование выходных данных команды `network list-usages`.
* В команде `application-gateway http-listener create` используется интерфейсный IP-адрес по умолчанию, если он существует.
* В команде `application-gateway rule create` используются пул адресов, параметры HTTP и прослушиватель HTTP по умолчанию, если они существуют.
* В команде `lb rule create` используются интерфейсный IP-адрес и серверный пул по умолчанию, если они существуют.
* В команде `lb inbound-nat-rule create` используется интерфейсный IP-адрес по умолчанию, если он существует.

### <a name="profile"></a>Профиль

* Поддержка входа на виртуальную машину с использованием управляемого удостоверения.
* Поддержка вывода данных команды `account show` в формате файла проверки подлинности с помощью пакета SDK.
* При использовании параметра --expanded-view отображаются предупреждения о прекращении поддержки.
* Добавлена команда `get-access-token` для предоставления необработанного токена AAD.
* Поддержка входа с учетной записью пользователя без связанных подписок.

### <a name="rdbms"></a>Реляционная СУБД

* Поддержка вывода списка серверов в подписке (3417).
* Исправлена проблема с обработкой `%s` из-за отсутствия `% server_type` (3393).
* Исправлено сопоставление источника документа и добавлена задача непрерывной интеграции для проверки (3361).
* Исправлена справка по MySQL и PostgreSQL (3369).

### <a name="resource"></a>Ресурс

* Улучшены запросы на ввод отсутствующих параметров для команды `group deployment create`.
* Улучшен анализ синтаксиса `--parameters KEY=VALUE`.
* Исправлены проблемы, из-за которых файлы параметров `group deployment create` не распознавались с использованием синтаксиса `@<file>`.
* Поддержка аргумента `--ids` в командах `resource` и `managedapp`.
* Исправлены некоторые сообщения об ошибках и анализе (3584).
* Исправлены ошибки анализа параметра `--resource-type` в команде `lock`. Теперь принимаются `<resource-namespace>` и `<resource-type>`.
* Добавлена проверка параметров для шаблонов для ссылок на шаблоны (3629).
* Добавлена поддержка указания параметров развертывания с использованием синтаксиса `KEY=VALUE`.

### <a name="role"></a>Роль

* Поддержка вывода данных команды `create-for-rbac` в формате файла проверки подлинности с помощью пакета SDK.
* Добавлена очистка назначений ролей и связанного приложения AAD при удалении субъекта-службы (3610).
* В описания аргументов `--start-date` и `--end-date` команды `app create` добавлен формат времени.
* При использовании параметра `--expanded-view` отображаются предупреждения о прекращении поддержки.
* Добавлена интеграция хранилища ключей для команд `create-for-rbac` и `reset-credentials`.

### <a name="service-fabric"></a>Service Fabric
* Исправлена ошибка, из-за которой большие файлы в приложениях усекались при отправке (3666).
* Добавлены тесты для команд Service Fabric (3424).
* Исправлены многие команды Service Fabric (3234).

### <a name="sql"></a>SQL

* Удален недействительный параметр `--identity` команды `sql server create`.
* Удалены значения паролей из выходных данных команд `sql server create` и `sql server update`.
* Добавлены команды `sql db list-editions` и `sql elastic-pool list-editions`.

### <a name="storage"></a>Память

* Удален параметр `--marker` из команд `storage blob list`, `storage container list` и `storage share list` (3745).
* Включено создание учетных записей хранения с поддержкой только HTTPS.
* Обновлены метрики хранилища, команды входа и CORS (3495).
* Перефразировано сообщение об исключении, которое выводит команда добавления CORS (3638) (3362)
* Генератор преобразован в список в режиме пробного выполнения команды пакетной загрузки (3592).
* Исправлена проблема при пробном выполнении команды пакетной загрузки больших двоичных объектов (3640) (3592).

### <a name="vm"></a>ВМ

* Поддержка настройки NSG.
* Исправлена ошибка, из-за которой не удавалось правильно настроить DNS-сервер.
* Поддержка удостоверений управляемой службы.
* Исправлена проблема, из-за которой для команды `cmss create` с существующей подсистемой балансировки нагрузки требовался параметр `--backend-pool-name`.
* Теперь нумерация LUN дисков данных, создаваемых с помощью команды `vm image create`, начинается с 0.


## <a name="may-10-2017"></a>10 мая 2017 г.

Версия 2.0.6

* Модуль documentdb переименован в cosmosdb.
* Добавлена реляционная СУБД (MySQL, Postgres).
* Добавлены модули Data Lake Store и Data Lake Analytics.
* Добавлен модуль Cognitive Services.
* Добавлен модуль Service Fabric.
* Добавлен модуль Interactive (az-shell переименован).
* Добавлена поддержка команд CDN.
* Удален модуль Container.
* Добавлена команда az -v для az --version ([#2926](https://github.com/Azure/azure-cli/issues/2926)).
* Повышена производительность загрузки пакетов и выполнения команд ([#2819](https://github.com/Azure/azure-cli/issues/2819)).

```text
azure-cli (2.0.6)

acr (2.0.4)
acs (2.0.6)
appservice (0.1.6)
batch (2.0.4)
cdn (0.0.2)
cloud (2.0.2)
cognitiveservices (0.1.2)
command-modules-nspkg (2.0.0)
component (2.0.4)
configure (2.0.6)
core (2.0.6)
cosmosdb (0.1.6)
dla (0.0.6)
dls (0.0.6)
feedback (2.0.2)
find (0.2.2)
interactive (0.3.1)
iot (0.1.5)
keyvault (2.0.4)
lab (0.0.4)
monitor (0.0.4)
network (2.0.6)
nspkg (3.0.0)
profile (2.0.4)
rdbms (0.0.1)
redis (0.2.3)
resource (2.0.6)
role (2.0.4)
sf (1.0.1)
sql (2.0.3)
storage (2.0.6)
vm (2.0.6)
```

### <a name="core"></a>Основные сведения

* Ядро: запись исключений, порожденных незарегистрированным поставщиком, и его автоматическая регистрация.
* Производительность: сохранение кэша маркеров библиотеки ADAL в памяти до завершения работы процесса ([#2603](https://github.com/Azure/azure-cli/issues/2603)).
* Исправление байтов, возвращаемых из шестнадцатеричных отпечатков -o tsv ([#3053](https://github.com/Azure/azure-cli/issues/3053)).
* Улучшенное скачивание сертификатов Key Vault и интеграция субъектов-служб AAD ([#3003](https://github.com/Azure/azure-cli/issues/3003)).
* Добавлено расположение Python в az -version ([#2986](https://github.com/Azure/azure-cli/issues/2986)).
* Вход: поддержка входа при отсутствии подписок ([#2929](https://github.com/Azure/azure-cli/issues/2929)).
* Ядро: устранен сбой при двукратном входе с помощью субъекта-службы ([#2800](https://github.com/Azure/azure-cli/issues/2800)).
* Ядро: возможность настроить путь к файлу accessTokens.json с помощью env var ([#2605](https://github.com/Azure/azure-cli/issues/2605)).
* Ядро: возможность применять настроенные параметры по умолчанию к необязательным аргументам ([#2703](https://github.com/Azure/azure-cli/issues/2703)).
* Ядро: повышение производительности.
* Ядро: настаиваемые сертификаты ЦС — поддержка настройки переменной среды REQUESTS_CA_BUNDLE.
* Ядро: облачная конфигурация — использование конечной точки Resource Manager, если не задана конечная точка управления.

### <a name="acs"></a>ACS

* Исправление: теперь значение счетчика баз данных master и агентов — целое число, а не строка.
* Предоставлены команды az acs create --no-wait и az acs wait для асинхронного создания.
* Предоставлена команда az acs create --validate для пробного создания.
* Удален профиль Windows перед вызовом метода PUT для команды scale ([#2755](https://github.com/Azure/azure-cli/issues/2755)).

### <a name="appservice"></a>AppService

* Приложение-функция: добавлена полная поддержка приложений-функций, включая создание, отображение, вывод списка, удаление, настройку имени узла, настройку протокола SSL и т. д.
* Добавлены службы Team Services (VSTS) в качестве параметра непрерывной доставки в конфигурации веб-системы управления версиями службы приложений.
* Создана команда az webapp, заменяющая команду az appservice web (для обеспечения обратной совместимости команда az appservice web будет оставлена еще в двух выпусках).
* Предоставлены аргументы для настройки развертывания и стеков времени выполнения при создании веб-приложения.
* Предоставлена команда webapp list-runtimes.
* Включена поддержка настройки строк подключения ([#2647](https://github.com/Azure/azure-cli/issues/2647)).
* Поддержка переключения слотов с предварительным просмотром.
* Устранены ошибки из команд службы приложений ([#2948](https://github.com/Azure/azure-cli/issues/2948)).
* Использование группы ресурсов в плане служб приложений для операций с сертификатами ([#2750](https://github.com/Azure/azure-cli/issues/2750)).

### <a name="cosmosdb"></a>Cosmos DB

* Модуль documentdb переименован в cosmosdb.
* Добавлена поддержка интерфейсов API уровня данных DocumentDB: управление базами данных и коллекциями.
* Добавлена поддержка включения автоматической отработки отказа для учетных записей базы данных.
* Добавлена поддержка нового параметра ConsistentPrefix политики согласованности.

### <a name="data-lake-analytics"></a>Data Lake Analytics

* Исправлена ошибка, из-за которой фильтрация списков заданий по результату и состоянию вызывала сбой.
* Добавлена поддержка нового типа элемента каталога — пакета. Для доступа к нему используется `az dla catalog package`.
* Появилась возможность вывести список следующих элементов каталога из базы данных (указание схемы не требуется):

  * Таблица
  * функция с табличным значением;
  * Представление
  * статистика таблицы. Их можно также вывести с помощью схемы, но без указания имени таблицы.

### <a name="data-lake-store"></a>Data Lake Storage

* Обновлена версия пакета SDK базовой файловой системы, что обеспечивает лучшую поддержку сценариев регулирования на стороне сервера.
* Повышена производительность загрузки пакетов и выполнения команд ([#2819](https://github.com/Azure/azure-cli/issues/2819)).
* Отсутствовала справка для команды access show. Теперь она добавлена. ([#2743](https://github.com/Azure/azure-cli/issues/2743))

### <a name="find"></a>Поиск

* Улучшены результаты поиска и разрешено управление версиями индекса поиска.

### <a name="keyvault"></a>Хранилище ключей

* BC: в команде `az keyvault certificate download` параметр -e со строкового или двоичного значения изменен на PEM или DER, чтобы лучше представить параметры.
* BC: из команды `keyvault certificate create` удалены параметры --expires и --not-before, так как они не поддерживаются службой.
* В команду `keyvault certificate create` добавлен параметр --validity для выборочного переопределения значение в параметре --policy.
* Исправлена ошибка в команде `keyvault certificate get-default-policy`, в которой были доступны параметры expires и not_before, а параметр validity_in_months — нет.
* Добавлено исправление для модуля keyvault для импорта PEM- и PFX-файлов ([#2754](https://github.com/Azure/azure-cli/issues/2754)).

### <a name="lab"></a>Лаборатория

* Добавлены команды создания, отображения, удаления и вывода списка для среды в лаборатории.
* Добавлены команды отображения и вывода списка для просмотра шаблонов ARM в лаборатории.
* В команду `az lab vm list` добавлен флаг --environment для фильтрации виртуальных машин по среде в лаборатории.
* Добавлена вспомогательная команда `az lab formula export-artifacts` для экспорта шаблона артефакта в формуле лаборатории.
* Добавлены команды для управления секретами в лаборатории.

### <a name="monitor"></a>Монитор

* Исправление ошибки. моделирование параметра `--actions` команды `az alert-rules create` для использования строки в формате JSON ([#3009](https://github.com/Azure/azure-cli/issues/3009)).
* Исправление ошибки: при создании параметров диагностики не принимались журналы и метрики из команды show ([#2913](https://github.com/Azure/azure-cli/issues/2913)).

### <a name="network"></a>Сеть

* Добавлена команда `network watcher test-connectivity`.
* Добавлена поддержка параметра `--filters` в команде `network watcher packet-capture create`.
* Добавлена поддержка фильтрации подключений шлюза приложений.
* Добавлена поддержка конфигурации набора правил WAF шлюза приложений.
* Добавлена поддержка правил и фильтров маршрутов ExpressRoute.
* Добавлена поддержка географической маршрутизации диспетчера трафика.
* Добавлена поддержка селекторов трафика на основе политик для VPN-подключений.
* Добавлена поддержка политик IPSec для VPN-подключений.
* Исправлена ошибка `vpn-connection create`, возникавшая при использовании параметра `--no-wait` или `--validate`.
* Добавлена поддержка шлюзов виртуальной сети "активный-активный".
* Удалены значения NULL из выходных данных команды `network vpn-connection list/show`.
* BC: исправлена ошибка в выходных данных `vpn-connection create`.
* Исправлена ошибка, приводившая к неправильному анализу аргумента --key-length команды vpn-connection create.
* Исправлена ошибка в `dns zone import`, из-за которой записи не импортировались правильно.
* Исправлена ошибка, из-за которой команда `traffic-manager endpoint update` не работала.
* Добавлены команды предварительного просмотра для Наблюдателя за сетями.

### <a name="profile"></a>Профиль

* Включена поддержка входа при отсутствии подписок ([#2560](https://github.com/Azure/azure-cli/issues/2560)).
* Включена поддержка сокращенных имен параметров в команде az account set --subscription ([#2980](https://github.com/Azure/azure-cli/issues/2980)).

### <a name="redis"></a>Redis

* Добавлена команда update, которая также добавляет возможность масштабирования для кэша Redis.
* Команда update-settings объявляется нерекомендуемой.

### <a name="resource"></a>Ресурс

* Добавлены команды определения managedapp и managedapp ([#2985](https://github.com/Azure/azure-cli/issues/2985)).
* Включена поддержка команд provider operation ([#2908](https://github.com/Azure/azure-cli/issues/2908)).
* Включена поддержка создания универсального ресурса ([#2606](https://github.com/Azure/azure-cli/issues/2606)).
* Исправлен анализ ресурсов и поиск версии API. ([#2781](https://github.com/Azure/azure-cli/issues/2781))
* Добавлены документы для команды az lock update. ([#2702](https://github.com/Azure/azure-cli/issues/2702))
* Исправлена ошибка, возникавшая при попытке вывести список ресурсов группы, которая не существует. ([#2769](https://github.com/Azure/azure-cli/issues/2769))
* [Вычисления.] Устранены проблемы с масштабируемым набором виртуальных машин и обновлением группы доступности виртуальных машин. ([#2773](https://github.com/Azure/azure-cli/issues/2773))
* Исправлена блокировка создания и удаления, возникающая, если параметр parent-resource-path не указан ([#2742](https://github.com/Azure/azure-cli/issues/2742)).

### <a name="role"></a>Роль

* create-for-rbac: гарантируется, что дата завершения работы поставщика служб не может превышать срок действия сертификата ([#2989](https://github.com/Azure/azure-cli/issues/2989)).
* RBAC: добавлена полная поддержка команды ad group ([#2016](https://github.com/Azure/azure-cli/issues/2016)).
* Роль: устранены проблемы при обновлении определения роли ([#2745](https://github.com/Azure/azure-cli/issues/2745)).
* create-for-rbac: обеспечен выбор введенного пользователем пароля.

### <a name="sql"></a>SQL

* Добавлены команды az sql server list-usages и az sql db list-usages.
* SQL: добавлена возможность прямого подключения к поставщику ресурса ([#2832](https://github.com/Azure/azure-cli/issues/2832)).

### <a name="storage"></a>Память

* Добавлено расположение по умолчанию группы ресурсов для `storage account create`.
* Добавлена поддержка добавочного копирования больших двоичных объектов.
* Добавлена поддержка передачи больших блочных BLOB-объектов.
* Размер блока изменяется и составляет 100 МБ, если передается файл, чей размер превышает 200 ГБ.

### <a name="vm"></a>ВМ

* avail-set: число доменов обновления и доменов сбоя сделано необязательным.

  Примечание. Команды виртуальной машины в независимых облаках: избегайте использовать функции, связанные с управляемыми дисками, включая следующие:
  1. az disk/snapshot/image
  2. az vm/vmss disk
  3. В команде az vm/vmss create используйте параметр --use-unmanaged-disk, чтобы избежать использования Управляемых дисков. Другие команды должны работать.
* vm/vmss: улучшен текст предупреждения при создании пары ключей SSH.
* vm/vmss: добавлена поддержка создания из образа Marketplace, для которого требуются сведения о плане ([#1209](https://github.com/Azure/azure-cli/issues/1209)).


## <a name="april-3-2017"></a>3 апреля 2017 г.

Версия 2.0.2

В этом выпуске мы добавили компоненты ACR, Batch, KeyVault и SQL.

```text
azure-cli (2.0.2)

acr (2.0.0)
acs (2.0.2)
appservice (0.1.2)
batch (2.0.0)
cloud (2.0.0)
component (2.0.0)
configure (2.0.2)
container (0.1.2)
core (2.0.2)
documentdb (0.1.2)
feedback (2.0.0)
find (0.0.1b1)
iot (0.1.2)
keyvault (2.0.0)
lab (0.0.1)
monitor (0.0.1)
network (2.0.2)
nspkg (2.0.0)
profile (2.0.2)
redis (0.1.1b3)
resource (2.0.2)
role (2.0.1)
sql (2.0.0)
storage (2.0.2)
vm (2.0.2)
```

### <a name="core"></a>Основные сведения

* Модули Acr, Lab, Monitor и Find добавлены в список по умолчанию.
* Вход: пропуск неправильного клиента ([#2634](https://github.com/Azure/azure-cli/pull/2634)).
* Вход: для подписки по умолчанию задано значение 1 с состоянием "Включено" ([#2575](https://github.com/Azure/azure-cli/pull/2575)).
* Добавлена поддержка команд wait и --no-wait для дополнительных команд ([#2524](https://github.com/Azure/azure-cli/pull/2524)).
* Core: поддержка входа при помощи субъекта-службы с использованием сертификата ([#2457](https://github.com/Azure/azure-cli/pull/2457)).
* Добавлен запрос для отсутствующих параметров шаблона ([#2364](https://github.com/Azure/azure-cli/pull/2364)).
* Добавлена поддержка установки параметров по умолчанию для таких распространенных аргументов, как группа ресурсов по умолчанию, веб-страница по умолчанию, виртуальная машина по умолчанию.
* Добавлена поддержка входа на конкретный клиент.

### <a name="acs"></a>ACS

* [ACS] Добавлена поддержка настройки кластера ACS по умолчанию ([#2554](https://github.com/Azure/azure-cli/pull/2554)).
* Добавлена поддержка запроса пароля для ключа SSH ([#2044](https://github.com/Azure/azure-cli/pull/2044)).
* Добавлена поддержка кластеров Windows ([#2211](https://github.com/Azure/azure-cli/pull/2211)).
* Добавлена возможность изменения роли "Владелец" на роль "Участник" ([#2321](https://github.com/Azure/azure-cli/pull/2321)).

### <a name="appservice"></a>AppService

* AppService: добавлена поддержка получения внешнего IP-адреса, используемого для записей DNS типа A ([#2627](https://github.com/Azure/azure-cli/pull/2627)).
* AppService: добавлена поддержка привязки групповых сертификатов ([#2625](https://github.com/Azure/azure-cli/pull/2625)).
* AppService: добавлена поддержка профилей для публикации списком ([#2504](https://github.com/Azure/azure-cli/pull/2504)).
* AppService: добавлен триггер синхронизации с системой управления версиями после настройки ([#2326](https://github.com/Azure/azure-cli/pull/2326)).

### <a name="datalake"></a>Data Lake

* Первый выпуск модуля Data Lake Analytics.
* Первый выпуск модуля Data Lake Store.

### <a name="docuemntdb"></a>DocumentDB

* DocumentDB: добавлена возможность получить список строк подключения ([#2580](https://github.com/Azure/azure-cli/pull/2580)).

### <a name="vm"></a>ВМ

* [Вычисления] Добавлена поддержка AppGateway для создания масштабируемого набора виртуальных машин ([#2570](https://github.com/Azure/azure-cli/pull/2570)).
* [ВМ и VMSS] Улучшена поддержка кэширования диска ([#2522](https://github.com/Azure/azure-cli/pull/2522)).
* ВМ и VMSS: внедрена логика проверки учетных данных, используемая на портале ([#2537](https://github.com/Azure/azure-cli/pull/2537)).
* Добавлена поддержка команд wait и --no-wait ([#2524](https://github.com/Azure/azure-cli/pull/2524)).
* Масштабируемый набор виртуальных машин: добавлена поддержка * для представления экземпляров на виртуальных машинах в виде списка ([#2467](https://github.com/Azure/azure-cli/pull/2467)).
* Добавлен параметр --secrets для виртуальной машины и масштабируемого набора виртуальных машин ([#2212}(<https://github.com/Azure/azure-cli/pull/2212>)).
* Добавлено разрешение на создание виртуальных машин на основе специализированного образа VHD ([#2256](https://github.com/Azure/azure-cli/pull/2256)).

## <a name="february-27-2017"></a>27 февраля 2017 г.

Версия 2.0.0

Этот первый общедоступный выпуск Azure CLI 2.0. Общедоступными являются такие командные модули:
- служба контейнеров (ACS);
- вычисления (в том числе Resource Manager, виртуальная машина, масштабируемые наборы виртуальных машин, управляемые диски);
- Сеть
- Память

Эти командные модули могут использоваться в рабочих средах. Они поддерживаются стандартными соглашениями Майкрософт об уровне обслуживания. Вы можете отправлять запросы непосредственно в службу технической поддержки Майкрософт или добавлять описание проблем в наш [список на сайте GitHub](https://github.com/azure/azure-cli/issues/). Вы можете задавать вопросы на [сайте StackOverflow, используя тег azure-cli](http://stackoverflow.com/questions/tagged/azure-cli), или обращаться к группе разработчиков по адресу электронной почты [azfeedback@microsoft.com](mailto:azfeedback@microsoft.com). Можно отправлять отзывы из командной строки с помощью команды `az feedback`.

Команды в этих модулях стабильны, и предполагается, что в следующих выпусках этой версии Azure CLI их синтаксис не будет меняться.

Проверить версию CLI можно с помощью команды `az --version`. В выходных данных указана версия самого интерфейса командной строки (2.0.0 в этом выпуске), версии отдельных командных модулей и используемые вами версии Python и GCC.

```text
azure-cli (2.0.0)

acs (2.0.0)
appservice (0.1.1b5)
batch (0.1.1b4)
cloud (2.0.0)
component (2.0.0)
configure (2.0.0)
container (0.1.1b4)
core (2.0.0)
documentdb (0.1.1b2)
feedback (2.0.0)
iot (0.1.1b3)
keyvault (0.1.1b5)
network (2.0.0)
nspkg (2.0.0)
profile (2.0.0)
redis (0.1.1b3)
resource (2.0.0)
role (2.0.0)
sql (0.1.1b5)
storage (2.0.0)
vm (2.0.0)

Python (Darwin) 2.7.10 (default, Jul 30 2016, 19:40:32)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.34)]
```

> [!Note]
> Некоторые командные модули имеют постфикс b*n* или rc*n*. Эти командные модули все еще находятся на стадии предварительной версии и станут общедоступными в будущем.

У нас также есть предварительные ежедневные сборки CLI. Дополнительные сведения см. в инструкциях по [получению ежедневных сборок](https://github.com/Azure/azure-cli#nightly-builds), а также в инструкциях по [настройке среды разработки и участии в написании кода](https://github.com/Azure/azure-cli#developer-setup).

О проблемах с предварительными ежедневными сборками можно сообщить несколькими способами:
- добавив информацию о проблемах в наш [список на сайте GitHub](https://github.com/azure/azure-cli/issues/);
- Свяжитесь с командой разработчиков ([azfeedback@microsoft.com](mailto:azfeedback@microsoft.com)),
- отправив отзыв из командной строки с помощью команды `az feedback`.
