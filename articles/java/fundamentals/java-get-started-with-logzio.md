---
title: Начало работы с Logz.io для приложений Java, выполняющихся в Azure
description: В этом руководстве показано, как интегрировать и настроить Logz.io для приложений Java, работающих в Azure.
author: jdubois
manager: bborges
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: judubois
ms.openlocfilehash: d7f90939701bfbcdcd895b8baf3cabcae4d5efa9
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81670760"
---
# <a name="tutorial-getting-started-with-monitoring-and-logging-using-logzio-for-java-apps-running-on-azure"></a>Руководство по началу работы с функциями мониторинга и ведения журналов с использованием Logz.io для приложений Java, работающих в Azure

В этом руководстве показано, как настроить классическое приложение Java, чтобы отправить журналы в службу [Logz.io](https://logz.io/) для обработки и анализа данных. Logz.io предоставляет полное решение для мониторинга на основе Elasticsearch, Logstash и Kibana (ELK), а также Grafana.

В этом руководстве предполагается, что вы используете Log4J или Logback. Эти библиотеки являются двумя наиболее широко используемыми ресурсами для ведения журнала в Java, поэтому учебник должен работать для большинства приложений, работающих в Azure. Если вы уже используете эластичный стек для мониторинга приложения Java, в этом руководстве показано, как настроить целевую конечную точку Logz.io.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Отправка журналов из существующего приложения Java в Logz.io.
> * Отправка журналов диагностики и метрик из служб Azure в Logz.io.

## <a name="prerequisites"></a>Предварительные требования

* [Java Developer Kit (JDK)](https://aka.ms/azure-jdks) версии 8 или более поздней
* Учетная запись Logz.io из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/logz.logzio-elk-as-a-service-pro).
* Существующее приложение Java, использующее Log4J или Logback

## <a name="send-java-application-logs-to-logzio"></a>Отправка журналов приложений Java в Logz.io

Во-первых, вы узнаете, как настроить приложение Java с помощью маркера, который предоставляет ему доступ к вашей учетной записи Logz.io.

### <a name="get-your-logzio-access-token"></a>Получение маркера доступа Logz.io

Чтобы получить маркер, войдите в свою учетную запись Logz.io, щелкните значок шестеренки в правом углу, а затем выберите **параметры > Общие**. Скопируйте маркер доступа, отображаемый в параметрах учетной записи, чтобы его можно было использовать позже.

### <a name="install-and-configure-the-logzio-library-for-log4j-or-logback"></a>Установка и настройка библиотеки Logz.io для Log4J или Logback

Библиотека Logz.io Java доступна в Maven Central, поэтому ее можно добавить в конфигурацию приложения в качестве зависимости. Проверьте номер версии в Maven Central и используйте последнюю версию в следующих параметрах конфигурации.

Если вы используете Maven, добавьте следующую зависимость в файл `pom.xml`:

**Log4J:**

```xml
<dependency>
    <groupId>io.logz.log4j2</groupId>
    <artifactId>logzio-log4j2-appender</artifactId>
    <version>1.0.11</version>
</dependency>
```

**Logback:**

```xml
<dependency>
    <groupId>io.logz.logback</groupId>
    <artifactId>logzio-logback-appender</artifactId>
    <version>1.0.22</version>
</dependency>
```

Если вы используете Gradle, добавьте следующую зависимость в сценарий сборки:

**Log4J:**

```
implementation 'io.logz.log4j:logzio-log4j-appender:1.0.11'
```

**Logback:**

```
implementation 'io.logz.logback:logzio-logback-appender:1.0.22'
```

Затем обновите файл конфигурации Log4J или Logback:

**Log4J:**

```xml
<Appenders>
    <LogzioAppender name="Logzio">
        <logzioToken><your-logz-io-token></logzioToken>
        <logzioType>java-application</logzioType>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
    </LogzioAppender>
</Appenders>

<Loggers>
    <Root level="info">
        <AppenderRef ref="Logzio"/>
    </Root>
</Loggers>
```

**Logback:**

```xml
<configuration>
    <!-- Use shutdownHook so that we can close gracefully and finish the log drain -->
    <shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
    <appender name="LogzioLogbackAppender" class="io.logz.logback.LogzioLogbackAppender">
        <token><your-logz-io-token></token>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
        <logzioType>java-application</logzioType>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="LogzioLogbackAppender"/>
    </root>
</configuration>
```

Замените заполнитель `<your-logz-io-token>` маркером доступа, а заполнитель — `<your-logz-io-listener-host>` именем узла прослушивателя для вашего региона (например, listener.logz.io). Дополнительные сведения о том, как узнать регион своей учетной записи, см. в статье [Account region](https://docs.logz.io/user-guide/accounts/account-region.html) (Регион учетной записи).

Элемент `logzioType` ссылается на логическое поле в Elasticsearch, которое используется для отделения разных документов друг от друга. Очень важно правильно настроить этот параметр, чтобы максимально эффективно использовать Logz.io.

Logz.io (тип) — ваш формат журнала (например: Apache, NGinx, MySQL), а не источник (например, server1, server2, server3). В этом руководстве мы вызываем тип `java-application`, поскольку настраиваем приложения Java, и планируем, что все приложения будут иметь одинаковый формат.

Для расширенного использования приложения Java можно сгруппировать в различные типы, которые имеют собственный формат журнала (можно настроить с помощью Log4J и Logback). Например, у вас может быть тип "spring-boot-monolith" и "spring-boot-microservice".

### <a name="test-your-configuration-and-log-analysis-on-logzio"></a>Проверка конфигурации и анализ журналов в Logz.io

После настройки библиотеки Logz.io приложение должно отправить журналы непосредственно в нее. Чтобы убедиться, что все работает правильно, перейдите в консоль Logz.io, выберите вкладку **Live tail**, а затем нажмите **Запустить**. Должно отобразиться сообщение, похожее на следующее, сообщающее, что подключение работает:

```output
Requesting Live Tail access...
Access granted. Opening connection...
Connected. Tailing...
````

Затем запустите приложение или используйте его для создания некоторых журналов. Журналы должны отображаться непосредственно на экране. Например, вот первые сообщения при запуске приложения Spring Boot:

```output
2019-09-19 12:54:40.685Z Starting JavaApp on javaapp-default-9-5cfcb8797f-dfp46 with PID 1 (/workspace/BOOT-INF/classes started by cnb in /workspace)
2019-09-19 12:54:40.686Z The following profiles are active: prod
2019-09-19 12:54:42.052Z Bootstrapping Spring Data repositories in DEFAULT mode.
2019-09-19 12:54:42.169Z Finished Spring Data repository scanning in 103ms. Found 6 repository interfaces.
2019-09-19 12:54:43.426Z Bean 'spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties' of type [org.springframework.boot.autoconfigure.task.TaskExecutionProperties] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
```

Теперь, когда журналы обрабатываются Logz.io, вы можете воспользоваться преимуществами всех служб платформы.

## <a name="send-azure-services-data-to-logzio"></a>Отправка данных служб Azure в Logz.io

Далее вы узнаете, как отправить журналы и метрики из ресурсов Azure в Logz.io.

### <a name="deploy-the-template"></a>Развертывание шаблона

Первым шагом является развертывание шаблона интеграции Logz.io — Azure. Интеграция основана на готовом шаблоне развертывания Azure, который настраивает все необходимые стандартные блоки конвейера. Шаблон создает пространство имен концентратора событий, концентратор событий, два больших двоичных объекта хранилища и все необходимые разрешения и соединения. Ресурсы, настроенные автоматическим развертыванием, могут получать данные для одного региона Azure и отправлять эти данные в Logz.io.

Найдите кнопку **Развернуть в Azure**, которая отображается в [первом шаге файла сведений о репозитории](https://github.com/logzio/logzio-azure-serverless).

При выборе **Развернуть в Azure** страница **Настраиваемое развертывание** на портале Azure будет отображаться со списком предварительно заполненных полей.

Большинство полей можно оставить в исходном виде, однако следующие параметры следует изменить.

* **Группа ресурсов.** Выберите существующую группу или создайте новую.
* **Узел метрик или журналов Logzio** Введите URL-адрес прослушивателя Logz.io. Если вы не знаете URL-адрес, проверьте URL-адрес для входа. Если это app.logz.io, используйте listener.logz.io (это значение по умолчанию). Если это app-eu.logz.io, используйте listener-eu.logz.io.
* **Маркер метрик или журналов Logzio** Введите маркер учетной записи Logz.io, в которую нужно поставлять журналы Azure или метрики. Этот маркер можно найти на странице учетной записи в пользовательском интерфейсе Logz.io.

Примите условия в нижней части страницы и выберите **Покупка**. Затем Azure развернет шаблон, что может занять одну или две минуты. В верхней части портала появится сообщение "Развертывание завершено".

Чтобы просмотреть развернутые ресурсы, можно посетить определенную группу ресурсов.

Чтобы узнать, как настроить logzio-azure-serverless для резервного копирования данных в хранилище BLOB-объектов Azure, см. статью [Ship Azure activity logs](https://docs.logz.io/shipping/log-sources/azure-activity-logs.html) (Отправка журналов действий Azure).

### <a name="stream-azure-logs-and-metrics-to-logzio"></a>Потоковая передача журналов и метрик Azure в Logz.io

Теперь, после развертывания шаблона интеграции, вам понадобится настроить Azure для потоковой передачи диагностических данных в только что развернутый концентратор событий. После передачи данных в концентратор событий, приложение-функция перенаправит эти данные в Logz.io.

1. В строке поиска введите "Диагностика", а затем выберите **Параметры диагностики**.

2. Выберите ресурс из списка ресурсов, а затем нажмите **Добавить параметр диагностики**, чтобы открыть панель**Параметры диагностики** для этого ресурса.

    ![Панель параметров диагностики](media/java-get-started-with-logzio/diagnostics-settings.png)

3. Присвойте параметрам диагностики **Имя**.

4. Выберите **Потоковая передача в концентратор событий**, затем выберите **Конфигурация**, чтобы открыть панель **Выбрать концентратор событий**.

5. Выберите свой концентратор событий.

    * **Выбор пространства имен концентратора событий**: Выберите пространство имен, которое начинается с **Logzio** (например,`LogzioNS6nvkqdcci10p`).
    * **Выбор имени концентратора событий**: Для журналов выберите **insights-operational-logs** а для метрик — **insights-operational-metrics**.
    * **Выбор имени политики концентратора событий**: Выберите **LogzioSharedAccessKey**.

6. Нажмите кнопку **ОК**, чтобы вернуться на панель **Параметры диагностики**.

7. В разделе "Журнал" выберите данные, которые необходимо передать в поток, а затем выберите **Сохранить**.

Теперь выбранные данные будут передаваться в концентратор событий.

### <a name="visualize-your-data"></a>Визуализация данных

Затем подождите некоторое время, чтобы получить данные из системы Logz.io, а затем откройте Kibana. Вы должны увидеть данные (с типом _eventhub_), которые заполняют панели мониторинга. Дополнительные сведения о создании панелей мониторинга см. в разделе [Creating the Perfect Kibana Dashboard](https://logz.io/blog/perfect-kibana-dashboard/) (Создание идеальной панели мониторинга Kibana).

После этого определенные данные можно запросить на вкладке **Обнаружение** или создать объекты Kibana для визуализации данных на вкладке **Визуализация**.

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с ресурсами Azure, созданными в этом руководстве, их можно удалить с помощью следующей команды:

```azurecli-interactive
az group delete --name <resource group>
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как настроить приложение Java и службы Azure для отправки журналов и метрик в Logz.io.

Далее, узнайте больше об использовании концентратора событий для мониторинга приложения.

> [!div class="nextstepaction"]
> [Потоковая передача данных мониторинга Azure в концентратор событий для потребления внешним инструментом](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)
