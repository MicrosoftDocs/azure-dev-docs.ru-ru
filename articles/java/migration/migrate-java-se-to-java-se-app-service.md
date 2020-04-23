---
title: Миграция приложений Java SE в Java SE в Службе приложений Azure
description: Узнайте о том, что следует учитывать при миграции существующего приложения Spring Boot или другого отдельного веб-приложения в Службу приложений Azure с использованием Java SE.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 9576a3831afb00b10b9bec3531c2dbc1dc60f29f
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672190"
---
# <a name="migrate-executable-jar-web-applications-to-java-se-on-azure-app-service"></a>Миграция веб-приложений на базе исполняемых JAR-файлов в Java SE в Службе приложений Azure

Узнайте о том, что следует учитывать при миграции существующего приложения Spring Boot или другого веб-приложения со встроенным сервером в Службу приложений Azure с использованием Java SE.

## <a name="before-you-start"></a>Перед началом работы

Если вы не можете выполнить какие-либо требования для подготовки к миграции, ознакомьтесь со следующими дополнительными руководствами по переносу:

* Перенос приложений на базе исполняемых JAR-файлов в контейнеры в Службе Azure Kubernetes (руководство ожидается)
* Перенос приложений на базе исполняемых JAR-файлов в Виртуальные машины Azure (руководство ожидается)

## <a name="pre-migration"></a>Подготовка к миграции

### <a name="switch-to-a-supported-platform"></a>Переход на поддерживаемую платформу

В Службе приложений доступны несколько версий Java SE. Чтобы обеспечить совместимость, перенесите приложение в одну из поддерживаемых версий текущей среды, прежде чем продолжать. Обязательно полностью протестируйте готовую конфигурацию. Используйте в этих тестах последний стабильный выпуск дистрибутива Linux.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="inventory-external-resources"></a>Проверка внешних ресурсов

Определите внешние ресурсы, в том числе источники данных, брокеры сообщений JMS и URL-адреса других служб. В приложениях Spring Boot файл с конфигурацией этих ресурсов обычно размещается в каталоге *src/main/directory* и называется *application.properties* или *application.yml*. Кроме того, проверьте наличие параметров конфигурации в переменных среды в рабочем развертывании.

#### <a name="databases"></a>Базы данных

Определите строки подключения ко всем базам данных SQL.

Для приложений Spring Boot строки подключения обычно хранятся в файлах конфигурации.

Вот пример из файла *application.properties*:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

Вот пример из файла *application.yml*:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

Дополнительные сведения см. в разделах о [репозиториях JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories) и [репозиториях JDBC](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories) в документации по Spring.

#### <a name="jms-message-brokers"></a>Брокеры сообщений JMS

Определите, какие брокеры используются. Для этого можно проверить наличие соответствующих зависимостей в манифесте сборки (обычно это файл *pom.xml* или *build.gradle*).

Например, в приложении Spring Boot, в котором используется ActiveMQ, эта зависимость обычно содержится в файле *pom.xml*:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

В приложениях Spring Boot, в которых используются собственные брокеры, зависимости обычно содержатся непосредственно в библиотеках драйверов брокера JMS. Вот пример из файла *build.gradle*:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```

Найдя все используемые брокеры, определите соответствующие параметры, которые в приложении Spring Boot обычно хранятся в файлах *application.properties* и *application.yml*.

Вот пример из файла *application.properties*:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Вот пример из файла *application.yml*:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

#### <a name="all-other-external-resources"></a>Другие связанные внешние ресурсы

Нет смысла описывать все возможные внешние зависимости, перечисленные в этом руководстве. Ваша команда должна убедиться в том, что после миграции в Службу приложений будут все внешние зависимости приложения будут доступными.

### <a name="inventory-secrets"></a>Проверка секретов

#### <a name="passwords-and-secure-strings"></a>Пароли и защищенные строки

Проверьте наличие секретных строк и паролей во всех свойствах и файлах конфигурации, а также всех переменных среды в рабочих развертываниях. В приложении Spring Boot такие строки обычно содержатся в файлах *application.properties* или *application.yml*.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [inventory-persistence-usage](includes/inventory-persistence-usage.md)]

### <a name="special-cases"></a>Особые случаи

В некоторых рабочих сценариях может потребоваться внести дополнительные изменения. Также могут применяться дополнительные ограничения. Хотя такие сценарии могут использоваться нечасто, важно убедиться, что приложение в них не задействовано или что они правильно реализуются.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Определение того, использует ли приложение запланированные задания

Запланированные задания, такие как задачи планировщика Quartz или задания Cron, нельзя использовать со Службой приложений. Служба приложений не будет препятствовать развертыванию приложения, содержащего запланированные задачи. Но если приложение масштабируется, одно запланированное задание может выполняться несколько раз в течение указанного периода. Это может привести к нежелательным последствиям.

Проверьте все запланированные задания, связанные с приложением (выполняющиеся и не выполняющиеся).

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Определение того, содержит ли приложение код, зависящий от ОС

Если в приложении есть код, зависящий от ОС, в которой работает это приложение, нужно выполнить рефакторинг этого приложения и УСТРАНИТЬ зависимости от базовой ОС. Например, все используемые символы `/` или `\` в путях файловой системы необходимо изменить на [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) или [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Определение всех внешних процессов и управляющих программ, запущенных на рабочих серверах

Все процессы (например, управляющие программы мониторинга), выполняющиеся за пределами сервера приложений, нужно перенести в другое расположение или завершить.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Определение обработки запросов, отличающихся от запросов HTTP или выполняющихся по нескольким портам

Служба приложений позволяет использовать только одну конечную точку HTTP и только один порт. Если приложение ожидает передачи данных по нескольким портам или принимает запросы, отличающиеся от запросов HTTP, не используйте Службу приложений Azure.

## <a name="migration"></a>Миграция

### <a name="parameterize-the-configuration"></a>Параметризация конфигурации

Убедитесь, что координаты всех внешних ресурсов (например, строк подключения к базам данных) и другие настраиваемые параметры считываются из переменных среды. В приложениях Spring Boot все параметры конфигурации должны быть вынесены во внешний код. Дополнительные сведения см. в разделе о [вынесении конфигурации во внешних код](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config) в документации по Spring Boot.

Вот пример со ссылкой на переменную среды `SERVICEBUS_CONNECTION_STRING` из файла *application.properties*:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Подготовка плана службы приложений

В [списке доступных планов обслуживания](https://azure.microsoft.com/pricing/details/app-service/linux/) выберите план, характеристики которого соответствуют требованиям к оборудованию для рабочей среды или превосходят их.

> [!NOTE]
> Если вы намерены использовать промежуточное или раннее развертывание либо [слоты развертывания](/azure/app-service/deploy-staging-slots), план службы приложений должен учитывать эту дополнительную емкость. Для приложений Java рекомендуется использовать план уровня "Премиум" или выше.

[Создайте план службы приложений](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Создание и развертывание веб-приложений

Вам нужно создать в плане службы приложений новое веб-приложение (со стеком времени выполнения Java SE) для каждого исполняемого JAR-файла, который вы хотите выполнить.

#### <a name="maven-applications"></a>Приложения Maven

Если приложение создано на основе POM-файла Maven, [используйте подключаемый модуль веб-приложения для Maven](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service), чтобы создать и развернуть веб-приложение.

#### <a name="non-maven-applications"></a>Приложения, отличающиеся от Maven

Если вы не можете использовать подключаемый модуль Maven, вам нужно будет подготовить веб-приложение с помощью других механизмов, например:

* [Портал Azure](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure CLI](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Создав веб-приложение, разверните его, используя один из [доступных механизмов развертывания](/azure/app-service/deploy-ftp). Если это возможно, приложение нужно передать в файл */home/site/wwwroot/app.jar*. Если вы не хотите переименовывать JAR-файл в app.jar, отправьте скрипт оболочки с командами для выполнения произвольного JAR-файла. Затем вставьте полный путь к этому скрипту в текстовое поле [Файл запуска](/azure/app-service/containers/app-service-linux-faq#built-in-images) в разделе конфигурации на портале. Скрипт запуска не выполняется в каталоге, в который он помещен. Поэтому всегда используйте абсолютные пути для создания ссылок на файлы в скрипте запуска (например, `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Перенос параметров среды выполнения JVM

Если для работы приложения требуются определенные параметры среды выполнения, [используйте наиболее подходящий механизм, чтобы указать их](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Миграция координат внешних ресурсов и других параметров

Выполните [эти действия для переноса строк подключения и других параметров](/azure/app-service/containers/configure-language-java#spring-boot-1).

> [!NOTE]
> Для всех параметров приложения Spring Boot, параметризованных с использованием переменных среды в разделе [параметризации конфигурации](#parameterize-the-configuration), эти переменные среды нужно определить в конфигурации приложения. Даже если параметры приложения Spring Boot явно не параметризованы, как описано выше, их можно соответствующим образом переопределить в конфигурации приложения. Пример:

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![Конфигурация приложений Службы приложений](media/migrate-java-se-to-java-se-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Перезапуск и тест состояния

Наконец, перезапустите веб-приложение, чтобы применить все изменения конфигурации. После завершения перезагрузки убедитесь, что приложение работает правильно.

## <a name="post-migration"></a>Действия после миграции

После переноса приложения в Службу приложений Azure нужно убедиться, что оно работает правильно. Затем вы можете применить некоторые рекомендации, которые помогут сделать ваше приложение более удобным для использования в облаке.

### <a name="recommendations"></a>Рекомендации

* Если для хранения файлов вы решили использовать каталог */home*, попробуйте [заменить его](/azure/app-service/containers/how-to-serve-content-from-azure-storage) службой хранилища Azure.

* При наличии в каталоге */home* конфигурации со строками подключения, ключами SSL и другими секретными сведениями, попробуйте использовать [Azure Key Vault](/azure/app-service/app-service-key-vault-references) в сочетании с [внедрением параметров с помощью параметров приложения](/azure/app-service/configure-common#configure-app-settings) или без него.

* Для предоставления надежных развертываний с нулевым временем простоя вы можете [использовать слоты развертывания](/azure/app-service/deploy-staging-slots).

* Разработайте и реализуйте стратегию DevOps. Чтобы обеспечить надежность и ускорить разработку, вы можете [автоматизировать развертывание и тестирование с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Если используются слоты развертывания, можно [автоматизировать развертывание в слоте](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) с последующим переключением слотов.

* Разработайте и реализуйте стратегии обеспечения непрерывности бизнес-процессов и аварийного восстановления. Для критически важных приложений вы можете использовать [архитектуру развертывания с несколькими регионами](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
