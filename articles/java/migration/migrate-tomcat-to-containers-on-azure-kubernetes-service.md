---
title: Перенос приложений Tomcat в контейнеры в Службе Azure Kubernetes
description: Узнайте, что следует учитывать при переносе существующего приложения Tomcat для запуска в контейнере Службы Azure Kubernetes.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 29e446c28cb6935ffe0eeb51fa3a4b21c93e78a4
ms.sourcegitcommit: 95fdc444c424f4a7d7d53437837e9532a0b897e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662965"
---
# <a name="migrate-tomcat-applications-to-containers-on-azure-kubernetes-service"></a>Перенос приложений Tomcat в контейнеры в Службе Azure Kubernetes

Узнайте, что следует учитывать при переносе существующего приложения Tomcat для запуска в контейнере Службы Azure Kubernetes (AKS).

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/inventory-secrets.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

<!-- AKS-specific addendum to inventory-persistence-usage -->
[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

### <a name="identify-session-persistence-mechanism"></a>Определение механизма сохранения сеанса

Чтобы узнать, какой используется диспетчер сохраняемости сеансов, изучите файлы *context.xml* в приложении и конфигурации Tomcat. Найдите элемент `<Manager>` и запишите значение атрибута `className`.

Встроенные реализации Tomcat [PersistentManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html), например [StandardManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Standard_Implementation) или [FileStore](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Nested_Components), не предназначены для использования с распределенной, масштабируемой платформой, такой как Kubernetes. AKS может распределять нагрузку между несколькими группами pod и прозрачно перезапускать любую группу в любое время, поэтому не рекомендуется сохранять изменяющееся состояние в файловой системе.

Если требуется сохранение сеанса, необходимо использовать альтернативную реализацию `PersistentManager`, которая будет выполнять запись во внешнее хранилище данных, например VMware Tanzu Session Manager с Redis Cache. См. сведения о том, как [использовать Redis в качестве кэша сеансов с Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Особые случаи

В некоторых рабочих сценариях может потребоваться внести дополнительные изменения. Также могут применяться дополнительные ограничения. Хотя такие сценарии могут использоваться нечасто, важно убедиться, что приложение в них не задействовано или что они правильно реализуются.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или задания Cron, нельзя использовать с контейнерными развертываниями Tomcat. Если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Проверьте все запланированные задания на сервере приложений или за его пределами.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Определение того, содержит ли приложение код, зависящий от ОС

Если приложение содержит код, поддерживающий ОС, в которой работает приложение, необходимо выполнить рефакторинг приложения, чтобы приложение НЕ зависело от базовой ОС. Например, все используемые символы `/` или `\` в путях файловой системы необходимо изменить на [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) или [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="determine-whether-memoryrealm-is-used"></a>Определение того, используется ли MemoryRealm

Для работы [MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) требуется сохраненный XML-файл. В Kubernetes этот файл должен быть добавлен в образ контейнера или передан в [общее хранилище, доступное контейнерам](#identify-session-persistence-mechanism). Необходимо соответствующим образом изменить параметр `pathName`.

Чтобы определить, используется ли сейчас `MemoryRealm`, изучите файлы *server.xml* и *context.xml* и найдите элементы `<Realm>`, в которых для атрибута `className` задано значение `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Определение того, используется ли отслеживание сеансов SSL

В контейнерных развертываниях сеансы SSL обычно разгружаются вне контейнера приложения. Как правило, это происходит с помощью контроллера входящего трафика. Если для работы приложения требуется [отслеживание сеансов SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL), убедитесь, что трафик SSL передается непосредственно в контейнер приложения.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Определение того, используется ли AccessLogValve

Если используется [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html), для параметра `directory` укажите [подключенную общую папку Файлов Azure](/azure/aks/azure-files-dynamic-pv) или один из подкаталогов.

### <a name="in-place-testing"></a>Тестирование на месте

Прежде чем создавать образы контейнеров, перенесите приложение в JDK и Tomcat для использования в AKS. Тщательно протестируйте приложение, чтобы обеспечить совместимость и высокую производительность.

### <a name="parameterize-the-configuration"></a>Параметризация конфигурации

В ходе предварительной миграции будут определены секреты и внешние зависимости, такие как источники данных, в файлах *server.xml* и *context.xml*. Для каждого определенного элемента измените имя пользователя, пароль, строку подключения или URL-адрес на переменную среды.

Например, предположим, что файл *context.xml* содержит следующий элемент:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

В этом случае его можно изменить, как показано в следующем примере.

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

## <a name="migration"></a>Миграция

Мы рекомендуем выполнить следующие действия для каждого приложения (WAR-файл), которое вы хотите перенести, за исключением первого шага — подготовки реестра контейнеров и AKS.

> [!NOTE]
> Некоторые развертывания Tomcat могут иметь несколько приложений, работающих на одном сервере Tomcat. Если это справедливо для вашего развертывания, мы настоятельно рекомендуем запускать каждое приложение в отдельной группе pod. Это позволяет оптимизировать использование ресурсов для каждого приложения, уменьшая сложность и сокращая степень взаимозависимости.

### <a name="provision-container-registry-and-aks"></a>Подготовка реестра контейнеров и AKS

Создайте реестр контейнеров и кластер Azure Kubernetes, субъект-служба которого имеет роль читателя в реестре. Не забудьте [выбрать соответствующую модель сети](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) в соответствии с требованиями к сети кластера.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```

### <a name="prepare-the-deployment-artifacts"></a>Подготовка артефактов развертывания

Клонируйте [репозиторий Tomcat on Containers QuickStart на GitHub](https://github.com/Azure/tomcat-container-quickstart). Он содержит файлы конфигурации Dockerfile и Tomcat с несколькими рекомендуемыми оптимизациями. В описанных ниже шагах мы рассмотрим изменения, которые следует внести в эти файлы, прежде чем создавать образ контейнера и развертывать его в AKS.

#### <a name="open-ports-for-clustering-if-needed"></a>Открытие портов для кластеризации (при необходимости)

Если вы планируете использовать [кластеризацию Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html) в AKS, убедитесь, что в Dockerfile открыты необходимые диапазоны портов. Чтобы указать IP-адрес сервера в файле *server.xml*, используйте значение переменной, инициализированной при запуске контейнера, в качестве IP-адреса группы pod.

Кроме того, состояние сеанса можно [хранить в альтернативном расположении](#identify-session-persistence-mechanism), чтобы оно было доступным в репликах.

Чтобы определить, использует ли приложение кластеризацию, найдите элемент `<Cluster>` в элементах `<Host>` или `<Engine>` в файле *server.xml*.

#### <a name="add-jndi-resources"></a>Добавление ресурсов JNDI

Измените файл *server.xml*, добавив в него ресурсы, подготовленные на шагах, выполняемых перед миграцией, например источники данных.

Пример:

```xml
<!-- Global JNDI resources
      Documentation at /docs/jndi-resources-howto.html
-->
<GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml"
               />

    <!-- Migrated datasources here: -->
    <Resource
        name="jdbc/dbconnection"
        type="javax.sql.DataSource"
        url="${postgresdb.connectionString}"
        driverClassName="org.postgresql.Driver"
        username="${postgresdb.username}"
        password="${postgresdb.password}"
    />
    <!-- End of migrated datasources -->
</GlobalNamingResources>
```

[!INCLUDE[Tomcat datasource additional instructions](includes/tomcat-datasource-additional-instructions.md)]

### <a name="build-and-push-the-image"></a>Сборка и отправка образа

Самый простой способ создать и передать образ в Реестр контейнеров Azure (ACR) для использования в AKS — с помощью команды `az acr build`. Для выполнения этой команды устанавливать Docker на компьютере не нужно. Например, если у вас есть соответствующий файл Dockerfile и пакет приложения *petclinic.war* в текущем каталоге, вы можете создать образ контейнера в ACR, выполнив один шаг.

```bash
az acr build -t "${acrName}.azurecr.io/petclinic:{{.Run.ID}}" -r $acrName --build-arg APP_FILE=petclinic.war --build-arg=prod.server.xml .
```

Можно опустить параметр `--build-arg APP_FILE...`, если WAR-файл называется *ROOT.war*. Можно опустить параметр `--build-arg SERVER_XML...`, если XML-файл сервера называется *server.xml*. Оба файла должны находиться в том же каталоге, что и *Dockerfile*.

Или же вы можете использовать Docker CLI, чтобы создать образ локально. Такой подход упрощает тестирование и дополнительную настройку образа перед первым развертыванием в ACR. Но для этого нужно установить Docker CLI и запустить управляющую программу Docker.

```bash
# Build the image locally
sudo docker build . --build-arg APP_FILE=petclinic.war -t "${acrName}.azurecr.io/petclinic:1"

# Run the image locally
sudo docker run -d -p 8080:8080 "${acrName}.azurecr.io/petclinic:1"

# Your application can now be accessed with a browser at http://localhost:8080.

# Log into ACR
sudo az acr login -n $acrName

# Push the image to ACR
sudo docker push "${acrName}.azurecr.io/petclinic:1"
```

См. дополнительные сведения о создании и хранении образов контейнеров в Azure в соответствующем [курсе Microsoft Learn](/learn/modules/build-and-store-container-images/).

### <a name="provision-a-public-ip-address"></a>Подготовка общедоступного IP-адреса

Если ваше приложение должно быть доступным за пределами внутренней или виртуальной сети, требуется общедоступный статический IP-адрес. Этот IP-адрес должен быть подготовлен в группе ресурсов узла кластера.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```

### <a name="deploy-to-aks"></a>Развертывание в AKS

[Создайте и примените файлы Kubernetes YAML](/azure/aks/kubernetes-walkthrough#run-the-application). Если вы создаете внешний балансировщик нагрузки (как для приложения, так и для входящего контроллера), обязательно укажите IP-адрес, подготовленный в предыдущем разделе, в качестве `LoadBalancerIP`.

Включите [внешние параметры в качестве переменных среды](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Не включайте секреты (например, пароли, ключи API и строки подключения JDBC). Сведения о секретах см. в разделе [Настройка хранилища ключей FlexVolume](#configure-keyvault-flexvolume).

### <a name="configure-persistent-storage"></a>Настройка постоянного хранилища

Если для работы приложения требуется энергонезависимое хранилище, настройте один или несколько [постоянных томов](/azure/aks/azure-disks-dynamic-pv).

Чтобы хранить журналы централизованно, вы можете создать постоянный том с помощью службы "Файлы Azure", подключенной к каталогу журналов Tomcat ( */tomcat_logs*). См. сведения о [динамическом создании и использовании постоянного тома с Файлами Azure в Службе Azure Kubernetes (AKS)](/azure/aks/azure-files-dynamic-pv).

### <a name="configure-keyvault-flexvolume"></a>Настройка хранилища ключей FlexVolume

[Создайте хранилище ключей в Azure](/azure/key-vault/quick-create-cli) и укажите все необходимые секреты. Затем настройте [хранилище ключей FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md), чтобы сделать эти секреты доступными для групп pod.

Вам нужно изменить скрипт запуска (*startup.sh* в репозитории [Tomcat on Containers](https://github.com/Azure/tomcat-container-quickstart) на GitHub), чтобы импортировать сертификаты в локальное хранилище ключей в контейнере.

### <a name="migrate-scheduled-jobs"></a>Перенос запланированных заданий

Чтобы выполнить запланированные задания в кластере AKS, определите требуемые [задания Cron](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в AKS нужно убедиться, что оно работает правильно. Затем вы можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

* Мы рекомендуем добавить DNS-имя к IP-адресу, выделенному для контроллера входящего трафика или балансировщика нагрузки приложения. Дополнительные сведения см. в статье о [создании контроллера входящего трафика со статическим общедоступным IP-адресом в AKS](/azure/aks/ingress-static-ip).

* Вы также можете [добавить чарты Helm для приложения](https://helm.sh/docs/topics/charts/). Чарты Helm позволяют параметризировать развертывание приложения для использования и настройки различными клиентами.

* Разработайте и реализуйте стратегию DevOps. Чтобы обеспечить надежность и ускорить разработку, вы можете [автоматизировать развертывание и тестирование с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Включите [мониторинг Azure для кластера](/azure/azure-monitor/insights/container-insights-enable-existing-clusters), чтобы разрешить сбор журналов контейнеров, отслеживание использования и т. д.

* Вы можете предоставить метрики конкретного приложения через Prometheus. Prometheus — это платформа метрик с открытым кодом, широко используемая в сообществе Kubernetes. Вы можете настроить [метрики Prometheus в Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration), чтобы не размещать собственный сервер Prometheus. Так вы включите агрегирование метрик из приложений и автоматическое реагирование или эскалацию при возникновении аномальных условий.

* Разработайте и реализуйте стратегии обеспечения непрерывности бизнес-процессов и аварийного восстановления. Для критически важных приложений вы можете использовать [архитектуру развертывания с несколькими регионами](/azure/aks/operator-best-practices-multi-region).

* Ознакомьтесь с [политикой поддержки версий Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). Вы должны [обновлять кластер AKS](/azure/aks/upgrade-cluster) самостоятельно, чтобы обеспечить его выполнение под управлением поддерживаемой версии.

* Предоставьте всем участникам группы, отвечающей за администрирование кластера и разработку приложений, доступ к соответствующим [рекомендациям по работе с AKS](/azure/aks/best-practices).

* Просмотрите элементы в файле *logging.properties*. Чтобы повысить производительность, вы можете удалить или сократить некоторые выходные данные журнала.

* Для дальнейшей оптимизации производительности вы можете [отслеживать размер кэша кода](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) и добавить параметры `-XX:InitialCodeCacheSize` и `-XX:ReservedCodeCacheSize` в переменную `JAVA_OPTS` в Dockerfile.
