---
title: Перекрестные ссылки между службами для работы с Azure CLI
description: Ссылки на популярные учебники, краткие руководства, примеры, основные понятия, практические руководства, Azure CLI, виртуальные машины, Azure Kubernetes Service (AKS), пакетная служба, Azure CLI (базовый), Azure Resource Manager, Key Vault, Azure Stack Hub, Функции, база данных, Центры событий, Конфигурация приложений, Германия, безопасность, Система управления, Аналитика, IoT, Интернет вещей, DevOps, Виртуальная сеть, Вычислительная среда, Сеть, средства для разработчиков, базы данных, аналитика, управление и система управления, гибридная среда, хранилище, безопасность, ИИ, искусственный интеллект и машинное обучение, Linux, Windows, Ubuntu, автоматизация, приложение, веб-приложение, скрипт
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 03/01/2020
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 648f147c8bc3f954f8b41c2fbec4500c3115e66a
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501279"
---
# <a name="popular-articles-using-the-azure-cli"></a>Популярные статьи, в которых используется Azure CLI

Azure CLI используется во многих службах Azure, поэтому статьи о нем распределены по разным репозиториям документов.  На этой странице представлены ссылки на самые популярные статьи.  

## <a name="compute"></a>Службы вычислений

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
|Виртуальные машины | Руководство по Linux | [Создание виртуальной машины Linux с помощью Azure CLI](azure-cli-vm-tutorial.yml) | Создайте виртуальную машину.  Сведения об исходящих запросах и настройке переменных среды.
|Виртуальные машины | Краткое руководство. Linux | [Создание виртуальной машины Linux с помощью Azure CLI](/azure/virtual-machines/linux/quick-create-cli) | Создайте и разверните виртуальную машину Linux.  Откройте порт для веб-трафика и установите веб-сервер.
|Виртуальные машины | Практическое руководство. Linux |[Создание образа Linux для виртуальной машины или виртуального жесткого диска](/azure/virtual-machines/linux/capture-image) | Отзовите существующую виртуальную машину, создайте образ и новую виртуальную машину из этого образа.
|Виртуальные машины | Практическое руководство. Linux | [Отправка виртуального жесткого диска в Azure с помощью Azure CLI](/azure/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli) | Создайте пустой управляемый диск, отправьте локальный VHD-файл и скопируйте управляемый диск.
|Виртуальные машины | Практическое руководство. Linux | [Создание общей коллекции образов с помощью Azure CLI](/azure/virtual-machines/linux/shared-images) | Создайте общую коллекцию образов для пользовательских образов виртуальных машин, чтобы предоставить их другим пользователям вашей организации в пределах региона, между регионами или в арендаторе Azure Active Directory.
|Виртуальные машины | Практическое руководство. Linux | [Развертывание точечных виртуальных машин с помощью Azure CLI (предварительная версия)](/azure/virtual-machines/linux/spot-cli) | Разверните точечную виртуальную машину Linux, которая не будет вытесняться по критерию цены.
|Виртуальные машины | Краткое руководство. Windows | [Создание виртуальной машины Windows с помощью Azure CLI](/azure/virtual-machines/windows/quick-create-cli) | Разверните в Azure виртуальную машину под управлением Windows Server 2016.
|Виртуальные машины | Ознакомиться с модулем | [Управление виртуальными машинами с помощью Azure CLI](https://docs.microsoft.com/learn/modules/manage-virtual-machines-with-azure-cli/) | Создайте, запустите, остановите виртуальную машину и попробуйте другие задачи управления для нее.
|Служба Azure Kubernetes (AKS)| Краткое руководство | [Развертывание кластера Службы Azure Kubernetes (AKS) с помощью Azure CLI](/azure/aks/kubernetes-walkthrough) | Разверните кластеры Службы контейнеров Azure и управляйте ими.  Также узнайте, как отслеживать работоспособность кластера и модулей pod, на которых выполняется приложение.
|Пакетная служба Azure|Образец | [Выполнение задания и задач с пакетной службой Azure с помощью Azure CLI](/azure/batch/scripts/batch-cli-sample-run-job) | Создайте задание пакетной службы и добавьте в него ряд задач. Отслеживайте выполнение задания и его задач.
|Пакетная служба Azure|Образец | [Создание пула Windows и управление им в пакетной службе Azure с помощью Azure CLI](/azure/batch/scripts/batch-cli-sample-manage-windows-pool) | Создайте пул вычислительных узлов Windows и управляйте им, используя конфигурацию Облачных служб.
|Экземпляр контейнера Azure|Краткое руководство | [Развертывание экземпляра контейнера в Azure с помощью Azure CLI](/azure/container-instances/container-instances-quickstart) | Разверните изолированный контейнер Docker и сделайте его приложение доступным по полному доменному имени (FQDN). Выполнив одну команду для развертывания, перейдите к приложению, которое выполняется в контейнере.
|функции Azure;|Краткое руководство |  [Создание функции в Azure, которая отвечает на HTTP-запросы, с помощью Azure CLI](/azure/azure-functions/functions-create-first-azure-function-azure-cli) | С помощью средств командной строки создайте функцию, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде разверните эту функцию в бессерверной среде Функций Azure.

## <a name="networking"></a>Сеть

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
|Виртуальная сеть|Краткое руководство | [Создание виртуальной сети с помощью интерфейса командной строки Azure](/azure/virtual-network/quick-create-cli) | Создайте виртуальную сеть, разверните в ней две виртуальные машины и подключитесь к ним из Интернета.
|Виртуальная сеть|Практическое руководство | [Включение функции ускорения сети на виртуальной машине Linux с помощью Azure CLI](/azure/virtual-network/create-vm-accelerated-networking-cli) | Создайте виртуальную машину Linux, организуйте динамическую привязку и отзыв виртуальной функции, а затем включите функцию ускорения сети.

## <a name="internet-of-things"></a>Интернет вещей

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
|Центр Интернета вещей|Учебник | [Руководство настройке маршрутизации сообщений Центра Интернета вещей с помощью Azure CLI](/azure/iot-hub/tutorial-routing) | Настройте и используйте пользовательские запросы маршрутизации для Центра Интернета вещей.

## <a name="developer-tools"></a>Средства для разработчиков

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
|конфигурация приложения Azure;|Примеры |[Примеры Azure CLI для службы "Конфигурация приложений Azure"](/azure/azure-app-configuration/cli-samples) | Получите ссылки на скрипты Bash, которые используют Azure CLI, для службы "Конфигурация приложений Azure".
|Azure DevOps| Начало работы. Конвейер DevOps |[Создание первого конвейера Azure с помощью Azure CLI](/azure/devops/pipelines/create-first-pipeline-cli) | Создайте конвейер в клонированном каталоге GitHub, запускайте конвейеры и управляйте ими.
|Azure DevOps| Практическое руководство. Конвейер DevOps |[Задачи развертывания конвейера Azure с помощью Azure CLI](/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops) | Запустите скрипт оболочки или пакетный сценарий, содержащий Azure CLI, в конвейере сборки или выпуска.  Команды выполняются в кроссплатформенных агентах в операционных системах Linux, macOS или Windows.
|Azure DevOps| Руководство по Конвейер Jenkins |[Развертывание в Службе приложений Azure с помощью Jenkins и Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) | Создайте и настройте виртуальную машину Jenkins, создайте веб-приложение в Azure и подготовьте репозиторий GitHub.  Создайте и запустите конвейер Jenkins.

## <a name="databases"></a>Базы данных

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
|База данных SQL| Образец |[Настройка Базы данных SQL Azure с помощью Azure CLI](/azure/sql-database/sql-database-cli-samples?tabs=single-database) | Примеры Azure CLI для Базы данных SQL Azure.
|MySQL|Краткое руководство |[Создание базы данных Azure для MySQL с помощью Azure CLI](/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli) | Создайте базу данных Azure для сервера MySQL.  Настройте правило брандмауэра и параметры SSL.  Получите и примените сведения о подключении.
|Cosmos DB |Практическое руководство |[Управление ресурсами Azure Cosmos с помощью Azure CLI](/azure/cosmos-db/manage-with-cli) | Примените типичные команды, позволяющие автоматизировать управление учетными записями, базами данных и контейнерами Azure Cosmos DB.
|Cosmos DB |Образец |[Примеры Azure CLI для API SQL (Core) Azure Cosmos DB](/azure/cosmos-db/cli-samples) | Получите ссылки на примеры скриптов Azure CLI для API SQL (Core) Azure Cosmos DB.

## <a name="analytics"></a>Analytics

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
концентратору событий Azure |Краткое руководство |[Создание концентратора событий с помощью Azure CLI](/azure/event-hubs/event-hubs-quickstart-cli) | Создайте пространство имен Центров событий и концентратор событий.
HDInsight |Руководство |[Создание кластеров HDInsight с помощью интерфейса командной строки Azure](/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli) | Создайте кластер HDInsight 3.6.
HDInsight |Учебник |[Управление кластерами Azure HDInsight с помощью Azure CLI](/azure/hdinsight/hdinsight-administer-use-command-line) | Сведения о получении списка, отображении, удалении и масштабировании кластеров HDInsight.

## <a name="management-and-governance"></a>Управление

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Шаблоны Resource Manager |Практическое руководство |[Развертывание ресурсов с использованием шаблонов Azure Resource Manager и Azure CLI](/azure/azure-resource-manager/templates/deploy-cli) | Разверните ресурсы в Azure с помощью шаблонов.
Группы Resource Manager |Практическое руководство |[Управление группами ресурсов Azure Resource Manager с помощью Azure CLI](/azure/azure-resource-manager/management/manage-resource-groups-cli) | Примените Azure CLI для управления группами ресурсов Azure.
Resource Graph |Краткое руководство |[Выполнение первого запроса Resource Graph с помощью Azure CLI](/azure/governance/resource-graph/first-query-azurecli) | Добавьте Azure Resource Graph в установку Azure CLI и выполните первый запрос Resource Graph.
Назначение политики |Краткое руководство |[Создание назначения политики для выявления ресурсов, не соответствующих требованиям, с помощью Azure CLI](/azure/governance/policy/assign-policy-azurecli) | Создайте назначение политики для выявления виртуальных машин, которые не используют управляемые диски.

## <a name="hybrid"></a>Гибридная среда

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Azure Stack Hub| Краткое руководство. Виртуальная машина Linux |[Создание виртуальной машины с сервером Linux в Azure Stack Hub с помощью Azure CLI](/azure-stack/user/azure-stack-quick-create-vm-linux-cli) | Создайте виртуальную машину Ubuntu Server 16.04 LTS, подключитесь к этой виртуальной машине с помощью удаленного клиента и установите веб-сервер NGINX.
Azure Stack Hub| Краткое руководство. Виртуальная машина Windows |[Создание виртуальной машины Windows Server с помощью Azure CLI в Azure Stack Hub](/azure-stack/user/azure-stack-quick-create-vm-windows-cli) |Создайте виртуальную машину Windows Server 2016, подключитесь к этой виртуальной машине с помощью удаленного клиента и установите веб-сервер IIS.
Azure Stack Hub| Практическое руководство. Ресурсы ASDK |[Развертывание и администрирование ресурсов в Azure Stack Hub с помощью Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2) | Настройте интерфейс командной строки Azure (CLI) для управления ресурсами Пакета средств разработки Azure Stack (ASDK) на клиентских платформах Linux, Mac и Windows.

## <a name="storage"></a>Память

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Хранилище BLOB-объектов |Краткое руководство |  [Создание, скачивание и получение списка больших двоичных объектов с помощью Azure CLI](/azure/storage/blobs/storage-quickstart-blobs-cli) | Отправьте данные в хранилище BLOB-объектов Azure и скачайте данные из этого хранилища.
Хранилище BLOB-объектов |Практическое руководство |[Авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](/azure/storage/common/authorize-data-operations-cli) | Настройте авторизацию операций с данными и переменные среды для нужных параметров.
Хранилище BLOB-объектов |Практическое руководство |[Использование Azure CLI для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения (предварительная версия)](/azure/storage/blobs/data-lake-storage-directory-file-acl-cli) | Создайте каталоги, файлы и разрешения в учетных записях хранения с иерархическим пространством имен, а также управляйте ими.
Хранилище файлов |Краткое руководство |[Создание общих папок Azure и управление ими с помощью Azure CLI](/azure/storage/files/storage-how-to-use-files-cli) | Создайте и примените общие папки Azure.  Создайте моментальные снимки общих ресурсов и управляйте ими.

## <a name="security"></a>Безопасность

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Субъект-служба |Практическое руководство |[Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli) | Создайте субъект-службу Azure, получите информацию о нем и выполните сброс с помощью Azure CLI.
RBAC |Практическое руководство |[Добавление и удаление назначений ролей с помощью Azure RBAC и Azure CLI](/azure/role-based-access-control/role-assignments-cli) | Присвойте роли для управления доступом на основе ролей в Azure.
Key Vault |Практическое руководство |[Управление Key Vault с помощью интерфейса командной строки Azure](/azure/key-vault/key-vault-manage-with-cli2) | Создайте хранилище Azure Key Vault и управляйте им.  Зарегистрируйте приложение и разрешения для него, настройте расширенные политики доступа и изучите команды кроссплатформенного интерфейса командной строки.
Key Vault |Учебник |[Управление ключами учетной записи хранения с помощью Key Vault и Azure CLI](/azure/key-vault/key-vault-ovw-storage-keys) | Управляйте ключами учетной записи хранения и создайте маркеры подписи для совместного доступа.

## <a name="ai--machine-learning"></a>Средства ИИ и машинного обучения

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Машинное обучение |Справочник |[Использование расширения Azure CLI для Машинного обучения Azure](/azure/machine-learning/reference-azure-machine-learning-cli) | Выполните эксперименты для создания моделей машинного обучения и зарегистрируйте эти модели для использования клиентами.  Упакуйте, разверните модели машинного обучения и отслеживайте их жизненный цикл.
Службы Cognitive Services |Руководство |[Создание ресурса Cognitive Services с помощью интерфейса командной строки Azure](/azure/cognitive-services/cognitive-services-apis-create-account-cli) | Зарегистрируйтесь в Azure Cognitive Services и создайте учетную запись с подпиской на одну или несколько служб.  Используйте созданные для вас ключи и конечную точку для аутентификации приложений.
Azure Monitor |Практическое руководство |[Создание рабочей области Log Analytics с помощью Azure CLI](/azure/azure-monitor/learn/quick-create-workspace-cli) | Создайте и настройте рабочую область Log Analytics.

## <a name="geographies"></a>Географические области

|Служба|Тип статьи|Заголовок статьи|Описание|
|-|-|-|-|
Azure для Германии |Приступая к работе |[Подключение к Azure для Германии с помощью Azure CLI](/azure/germany/germany-get-started-connect-with-cli) | Используйте Azure для Германии для управления большой подпиской с помощью скриптов и функций доступа, которые пока недоступны на глобальном портале Azure.
Azure для государственных организаций|Приступая к работе |[Подключение к Azure для государственных организаций с помощью Azure CLI](/azure/azure-government/documentation-government-get-started-connect-with-cli)|Получите доступ к ресурсам в регионе Azure для государственных организаций и управляйте ими.

## <a name="see-also"></a>См. также раздел

* [Начало работы с Azure CLI](get-started-with-azure-cli.md)
* [Полный список справочников по командам Azure CLI](/cli/azure/reference-index)
* [Службы, которыми можно управлять с помощью Azure CLI](azure-services-the-azure-cli-can-manage.md)
