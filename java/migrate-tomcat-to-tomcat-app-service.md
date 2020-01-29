---
title: Перенос приложений Tomcat в Tomcat в Службе приложений Azure
description: Узнайте, что следует учитывать при переносе существующего приложения Tomcat для запуска в Службе приложений Azure с использованием Tomcat.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: ce1c54f0f4b28c5c0a2e11f4afc53f1dd59899c5
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288603"
---
# <a name="migrate-tomcat-applications-to-tomcat-on-azure-app-service"></a>Перенос приложений Tomcat в Tomcat в Службе приложений Azure

Узнайте, что следует учитывать при переносе существующего приложения Tomcat для запуска в Службе приложений Azure с использованием Tomcat 8.5 или 9.0.

## <a name="before-you-start"></a>Перед началом работы

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь со следующими дополнительными руководствами по переносу:

* [Перенос приложений Tomcat в контейнеры в Службе Azure Kubernetes](migrate-tomcat-to-containers-on-azure-kubernetes-service.md)
* Migrate Tomcat Applications to Azure Virtual Machines (Перенос приложений Tomcat на виртуальные машины Azure) (ожидается)

## <a name="pre-migration-steps"></a>Шаги по подготовке к миграции

* [Переход на поддерживаемую платформу](#switch-to-a-supported-platform)
* [Проверка внешних ресурсов](#inventory-external-resources)
* [Проверка секретов](#inventory-secrets)
* [Проверка использования сохраняемости](#inventory-persistence-usage)
* [Особые случаи](#special-cases)

### <a name="switch-to-a-supported-platform"></a>Переход на поддерживаемую платформу

В Службе приложений доступны некоторые версии Tomcat на основе определенных версий Java. Чтобы обеспечить совместимость, перенесите приложение в одну из поддерживаемых версий Tomcat и Java в текущей среде, прежде чем переходить к остальным действиям. Обязательно полностью протестируйте готовую конфигурацию. В таких тестах в качестве операционной системы используйте [Red Hat Enterprise Linux 8](https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux80-ARM).

#### <a name="java"></a>Java

> [!NOTE]
> Эта проверка особенно важна, если на текущем сервере используется неподдерживаемая версия JDK (например, Oracle JDK или IBM OpenJ9).

Чтобы получить текущую версию Java, войдите на сервер в рабочей среде и выполните следующую команду:

```bash
java -version
```

Чтобы получить текущую версию, используемую в Службе приложений Azure, скачайте [Zulu 8](https://www.azul.com/downloads/zulu-community/?&version=java-8-lts&os=&os=linux&architecture=x86-64-bit&package=jdk), если вы планируете использовать среду выполнения Java 8, или [Zulu 11](https://www.azul.com/downloads/zulu-community/?&version=java-11-lts&os=&os=linux&architecture=x86-64-bit&package=jdk), если вы планируете использовать среду выполнения Java 11.

#### <a name="tomcat"></a>Tomcat

Чтобы узнать текущую версию Tomcat, войдите на сервер в рабочей среде и выполните следующую команду:

```bash
${CATALINA_HOME}/bin/version.sh
```

Чтобы получить текущую версию, поддерживаемую в Службе приложений Azure, скачайте [Tomcat 8.5](https://tomcat.apache.org/download-80.cgi#8.5.50) или [Tomcat 9](https://tomcat.apache.org/download-90.cgi) в зависимости от того, какую версию вы планируете использовать в Службе приложений Azure.

[!INCLUDE [inventory-external-resources](includes/migration/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/migration/inventory-secrets.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

<!-- App-Service-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Динамическое или внутреннее содержимое

Для использования файлов, которые приложение часто записывает и считывает (например, временные файлы данных), или статических файлов, видимых только для вашего приложения, вы можете подключить службу хранилища Azure к файловой системе Службы приложений. См. сведения в руководстве по [обработке содержимого из службы хранилища Azure в Службе приложений в Linux](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

### <a name="identify-session-persistence-mechanism"></a>Определение механизма сохранения сеанса

Чтобы узнать, какой используется диспетчер сохраняемости сеансов, изучите файлы *context.xml* в приложении и конфигурации Tomcat. Найдите элемент `<Manager>` и запишите значение атрибута `className`.

Встроенные реализации Tomcat [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html), например [StandardManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Standard_Implementation) или [FileStore](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Nested_Components), не предназначены для использования с распределенной, масштабируемой платформой, такой как Служба приложений. Так как Служба приложений может распределять нагрузку между несколькими экземплярами и прозрачно перезапускать любой экземпляр в любое время, не рекомендуется сохранять изменяющееся состояние в файловой системе.

Если требуется сохранение сеанса, необходимо использовать альтернативную реализацию `PersistentManager`, которая будет выполнять запись во внешнее хранилище данных, например Pivotal Session Manager с Redis Cache. См. сведения о том, как [использовать Redis в качестве кэша сеансов с Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Особые случаи

В некоторых рабочих сценариях может потребоваться внести дополнительные изменения. Также могут применяться дополнительные ограничения. Хотя такие сценарии могут использоваться нечасто, важно убедиться, что приложение в них не задействовано или что они правильно реализуются.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или задания Cron, нельзя использовать со Службой приложений. Служба приложений не будет препятствовать развертыванию приложения, содержащего запланированные задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Проверьте все запланированные задания на сервере приложений или за его пределами.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Определение того, содержит ли приложение код, зависящий от ОС

Если приложение содержит код, зависящий от ОС узла, вам нужно выполнить рефакторинг кода для удаления этих зависимостей. Например, вам нужно заменить все символы `/` или `\`, используемые в путях файловой системы, на [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) или [`Paths.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="determine-whether-tomcat-clustering-is-used"></a>Определение того, используется ли кластеризация Tomcat

[Кластеризация Tomcat](https://tomcat.apache.org/tomcat-8.5-doc/cluster-howto.html) не поддерживается в Службе приложений Azure. Вместо этого можно настроить масштабирование и балансировку нагрузки и управлять ею в Службе приложений Azure без использования специальных функций Tomcat. Можно хранить состояние сеанса в альтернативном расположении, чтобы оно было доступным для всех реплик. См. сведения о том, как [определить механизм сохранения сеанса](#identify-session-persistence-mechanism).

Чтобы определить, использует ли приложение кластеризацию, найдите элемент `<Cluster>` в элементах `<Host>` или `<Engine>` в файле *server.xml*.

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Определение всех внешних процессов и управляющих программ, запущенных на рабочих серверах

Вам нужно будет перенести в другое расположение или удалить все процессы (например, управляющие программы мониторинга), выполняющиеся за пределами сервера приложений.

#### <a name="determine-whether-non-http-connectors-are-used"></a>Определение того, используются ли соединители, отличающиеся от HTTP

Служба приложений позволяет использовать только один соединитель HTTP. Если для работы приложения требуются дополнительные соединители, например соединитель AJP, не используйте Службу приложений.

Чтобы определить соединители HTTP, используемые приложением, найдите элементы `<Connector>` в файле *server.xml* в конфигурации Tomcat.

#### <a name="determine-whether-memoryrealm-is-used"></a>Определение того, используется ли MemoryRealm

Для работы [MemoryRealm](https://tomcat.apache.org/tomcat-8.5-doc/api/org/apache/catalina/realm/MemoryRealm.html) требуется сохраненный XML-файл. В Службе приложений Azure передайте этот файл в каталог */home* или его подкаталог либо в подключенное хранилище. Измените параметр `pathName` соответствующим образом.

Чтобы определить, используется ли сейчас `MemoryRealm`, изучите файлы *server.xml* и *context.xml* и найдите элементы `<Realm>`, в которых для атрибута `className` задано значение `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Определение того, используется ли отслеживание сеансов SSL

Служба приложений выполняет разгрузку сеансов за пределами среды выполнения Tomcat. Поэтому нельзя использовать [отслеживание сеансов SSL](https://tomcat.apache.org/tomcat-8.5-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL). Используйте другой режим отслеживания сеансов (`COOKIE` или `URL`). Если требуется применить отслеживание сеансов SSL, не используйте Службу приложений.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Определение того, используется ли AccessLogValve

Если используется [AccessLogValve](https://tomcat.apache.org/tomcat-8.5-doc/api/org/apache/catalina/valves/AccessLogValve.html), укажите для параметра `directory` значение `/home/LogFiles` или подкаталог.

## <a name="migration"></a>Миграция

### <a name="parametrize-the-configuration"></a>Параметризация конфигурации

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

### <a name="provision-an-app-service-plan"></a>Подготовка плана службы приложений

В списке доступных планов обслуживания на странице с [ценами на Службу приложений](https://azure.microsoft.com/pricing/details/app-service/linux/) выберите план, характеристики которого соответствуют или превосходят требования оборудования для текущей рабочей среды.

> [!NOTE]
> Если планируется выполнять промежуточное или раннее развертывание либо использовать слоты развертывания, план службы приложений должен включать дополнительную емкость. Для приложений Java рекомендуется использовать план уровня "Премиум" или выше. См. сведения о том, как [настраивать промежуточные среды в Службе приложений Azure](/azure/app-service/deploy-staging-slots).

Затем создайте план службы приложений. См. сведения о том, как [управлять планом службы приложений в Azure](/azure/app-service/app-service-plan-manage).

### <a name="create-and-deploy-web-apps"></a>Создание и развертывание веб-приложений

Для каждого WAR-файла, развернутого на сервере Tomcat, необходимо создать веб-приложение в плане службы приложений.

> [!NOTE]
> Вы можете развернуть несколько WAR-файлов в одном веб-приложении, но это крайне нежелательно. Если развернуть несколько WAR-файлов в одном веб-приложении, приложения не смогут масштабироваться в соответствии с требованиями к их использованию. Кроме того, это усложнит выполнение последующих конвейеров развертывания. Если несколько приложений должны быть доступны по одному URL-адресу, попробуйте использовать решение для маршрутизации, например [Шлюз приложений Azure](/azure/application-gateway/).

#### <a name="maven-applications"></a>Приложения Maven

Если приложение создано на основе POM-файла Maven, [используйте подключаемый модуль веб-приложения для Maven](/azure/app-service/containers/quickstart-java#configure-the-maven-plugin), чтобы создать и развернуть веб-приложение.

#### <a name="non-maven-applications"></a>Приложения, отличающиеся от Maven

Если вы не можете использовать подключаемый модуль Maven, вам нужно будет подготовить веб-приложение с помощью других механизмов, например:

* [Портал Azure](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure CLI](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Создав веб-приложение, разверните его, используя один из [доступных механизмов развертывания](/azure/app-service/deploy-zip).

### <a name="migrate-jvm-runtime-options"></a>Перенос параметров среды выполнения JVM

Если для работы приложения требуются определенные параметры среды выполнения, [используйте наиболее подходящий механизм, чтобы указать их](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

### <a name="populate-secrets"></a>Указание секретов

Чтобы сохранить все секреты, относящиеся к вашему приложению, используйте параметры приложения. Либо используйте [Azure Key Vault](/azure/app-service/containers/configure-language-java#use-keyvault-references), если вы планируете применять одни и те же секреты в нескольких приложениях или реализовать политики для точного управления доступом и возможности аудита.

### <a name="configure-custom-domain-and-ssl"></a>Настройка личного домена и SSL

Если приложение будет отображаться в личном домене, вам нужно будет [сопоставить с ним веб-приложение](/azure/app-service/app-service-web-tutorial-custom-domain).

После этого потребуется [привязать SSL-сертификат для этого домена к веб-приложению в Службе приложений](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="migrate-data-sources-libraries-and-jndi-resources"></a>Перенос источников данных, библиотек и ресурсов JNDI

Чтобы перенести источники данных, выполните [эти действия](/azure/app-service/containers/configure-language-java#tomcat).

Перенесите все дополнительные зависимости classpath уровня сервера, выполнив [те же действия, что и для JAR-файлов источников данных](/azure/app-service/containers/configure-language-java#finalize-configuration).

Перенесите все дополнительные [общие ресурсы JDNI уровня сервера](/azure/app-service/containers/configure-language-java#shared-server-level-resources).

> [!NOTE]
> Если вы используете рекомендуемую архитектуру "один WAR-файл на одно веб-приложение", перенесите библиотеки classpath и ресурсы JNDI на уровне сервера в приложение. Это значительно упростит управление компонентами и изменениями.

### <a name="migrate-remaining-configuration"></a>Перенос оставшейся конфигурации

Когда вы выполните инструкции из предыдущего раздела, настраиваемая конфигурация сервера должна будет находится в папке */home/tomcat/conf*.

Завершите перенос, скопировав все дополнительные конфигурации (например, [realms](https://tomcat.apache.org/tomcat-8.5-doc/config/realm.html) и [JASPIC](https://tomcat.apache.org/tomcat-8.5-doc/config/jaspic.html)).

### <a name="migrate-scheduled-jobs"></a>Перенос запланированных заданий

Для выполнения запланированных заданий в Azure рекомендуется использовать [Функции Azure с триггером таймера](/azure/azure-functions/functions-bindings-timer). Переносить сам код задания в функцию не нужно. Функция может просто вызвать URL-адрес в приложении, чтобы активировать задание.

Либо создайте [приложение логики](/azure/logic-apps/logic-apps-overview) с [триггером повторения](/azure/logic-apps/tutorial-build-schedule-recurring-logic-app-workflow#add-the-recurrence-trigger) для вызова URL-адреса без необходимости писать код за пределами приложения.

> [!NOTE]
> Чтобы предотвратить вредоносное использование, необходимо убедиться, что конечная точка вызова задания запрашивает ввод учетных данных. В этом случае функция для триггеров должна предоставить учетные данные.

### <a name="restart-and-smoke-test"></a>Перезапуск и тест состояния

Наконец, перезапустите веб-приложение, чтобы применить все изменения конфигурации. После завершения перезагрузки убедитесь, что приложение работает правильно.

## <a name="post-migration-steps"></a>Действия после переноса данных

После переноса приложения в Службу приложений Azure нужно убедиться, что оно работает правильно. Затем вы можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

### <a name="recommendations"></a>Рекомендации

1. Если для хранения файлов вы решили использовать каталог */home*, попробуйте [заменить его](/azure/app-service/containers/how-to-serve-content-from-azure-storage) службой хранилища Azure.

1. При наличии в каталоге */home* конфигурации, содержащей строки подключения, ключи SSL и другие секретные сведения, по возможности используйте [Azure Key Vault](/azure/app-service/app-service-key-vault-references) в сочетании с [внедрением параметров с помощью параметров приложения](/azure/app-service/configure-common#configure-app-settings) или без него.

1. Для предоставления надежных развертываний с нулевым временем простоя вы можете [использовать слоты развертывания](/azure/app-service/deploy-staging-slots).

1. Разработайте и реализуйте стратегию DevOps. Чтобы обеспечить надежность и ускорить разработку, вы можете [автоматизировать развертывание и тестирование с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Если используются слоты развертывания, можно [автоматизировать развертывание в слоте](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) с последующим переключением слотов.

1. Разработайте и реализуйте стратегии обеспечения непрерывности бизнес-процессов и аварийного восстановления. Для критически важных приложений вы можете использовать [архитектуру развертывания с несколькими регионами](/azure/architecture/reference-architectures/app-service-web-app/multi-region).