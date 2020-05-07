---
title: Настройка ведения журнала с пакетом Azure SDK для Java
description: Узнайте, как настроить платформы ведения журналов для клиентских библиотек Java в Azure SDK
keywords: Azure, Java, SDK, ведение журнала
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9f7ad8d772b7de1335ebc3ba77cdea8aa1e8b683
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81671950"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Настройка ведения журнала с пакетом Azure SDK для Java

В этой статье приводятся примеры настройки ведения журналов для [пакета SDK для Azure](https://azure.microsoft.com/downloads/) для Java. Дополнительные сведения о параметрах конфигурации, таких как настройка уровней ведения журнала или пользовательское ведение журналов по классам, см. в документации выбранной платформы ведения журналов.

Пакет Azure SDK для клиентских библиотек Java использует [Simple Logging Facade for Java](https://www.slf4j.org/) (SLF4J). SLF4J позволяет использовать предпочтительную платформу ведения журналов, которая вызывается во время развертывания приложения.

> [!NOTE]
> Эта статья применима к последним версиям клиентских библиотек пакета Azure SDK. Чтобы узнать, поддерживается ли библиотека, обратитесь к списку [последних выпусков Azure SDK](https://azure.github.io/azure-sdk/releases/latest/java.html). Если приложение использует более раннюю версию клиентских библиотек пакета Azure SDK, ознакомьтесь с конкретными инструкциями в соответствующей документации службы.

## <a name="declare-a-logging-framework"></a>Объявление платформы ведения журналов

Перед реализацией этих средств ведения журнала необходимо объявить соответствующую платформу в качестве зависимости в проекте. Дополнительные сведения см. в [руководстве пользователя по SLF4J](http://www.slf4j.org/manual.html#projectDep).

## <a name="configure-log4j-or-log4j-2"></a>Настройка log4j или log4j 2

Вы можете настроить ведение журнала log4j и log4j 2 в файле свойств или в XML-файле. Подробные сведения о ведении журнала log4j и log4j 2 см. в [руководстве по Apache Log4j 2](https://logging.apache.org/log4j/2.x/manual/configuration.html).

### <a name="use-a-properties-file"></a>Использование файла свойств

В каталоге проекта *./src/main/resource* создайте новый файл с именем *log4j.properties* или *log4j2.properties* (последний для Logj4 2). Используйте эти примеры, чтобы приступить к работе.

Пример log4j:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

Пример Log4j2:

```properties
appender.console.type = Console
appender.console.name = LogToConsole
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR
```

### <a name="use-an-xml-file"></a>Использование XML-файла

Кроме того, для настройки log4j и Log4j2 можно использовать XML-файл. В каталоге проекта *./src/main/resource* создайте новый файл с именем *log4j.xml* или *log4j2.xml* (последний для Logj4 2). Используйте эти примеры, чтобы приступить к работе.

Пример log4j:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

  <appender name="console" class="org.apache.log4j.ConsoleAppender">
    <param name="Target" value="System.out"/>
    <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%m%n" />
    </layout>
  </appender>
  <logger name="com.azure.core" additivity="true">
    <level value="ERROR" />
    <appender-ref ref="console" />
  </logger>

  <root>
    <priority value ="info"></priority>
    <appender-ref ref="console"></appender>
  </root>

</log4j:configuration>
```

Пример Log4j2:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.azure.core" level="error" additivity="true">
            <appender-ref ref="console" />
        </Logger>
        <Root level="info" additivity="false">
            <appender-ref ref="console" />
        </Root>
     </Loggers>
</Configuration>
```

## <a name="configure-logback"></a>Настройка Logback

[Logback](https://logback.qos.ch/manual/introduction.html) — одна из популярных платформ ведения журналов и собственная реализация SLF4J. Чтобы настроить Logback, создайте новый XML-файл с именем *logback.xml* в каталоге проекта *./src/main/resources*. Дополнительные сведения о параметрах конфигурации можно найти на веб-сайте [Logback Project](https://logback.qos.ch/manual/configuration.html).

Ниже приведен пример конфигурации Logback.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%message%n</pattern>
    </encoder>
  </appender>

  <logger name="com.azure.core" level="ERROR" />

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

Вот простая конфигурация Logback для входа в консоль:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="Console"
    class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="Console" />
  </root>
</configuration>
```

Ниже приведена конфигурация для ведения журнала в файл, который переносится после каждого часа и архивируется в формате GZIP:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder
      class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="info">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="configure-logback-for-a-spring-boot-application"></a>Настройка Logback для приложения Spring Boot

Spring ищет конфигурации проекта, включая ведение журнала, в файле *application.properties*, который находится в каталоге *./src/main/resources*. Добавьте следующую строку в файле *application.properties*, чтобы связать *logback.xml* с приложением Spring Boot:

```properties
logging.config=classpath:logback.xml
```

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Настройка резервного журнала для временной отладки

В сценариях, где невозможно повторно развернуть приложение с помощью средства ведения журнала SLF4J, можно использовать резервное средство ведения журнала, встроенное в клиентские библиотеки Azure для Java, в Azure Core 1.3.0 или более поздней версии. Чтобы включить это средство ведения журнала, необходимо сначала убедиться в отсутствии средства ведения журнала SLF4J (так как оно будет иметь приоритет), а затем задать переменную среды `AZURE_LOG_LEVEL`. После задания переменной среды, чтобы начать создание журналов необходимо перезапустить приложение.

В следующей таблице приведены допустимые значения для этой переменной среды.

|Уровень ведения журнала   |Разрешенные значения переменных среды   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATIONAL|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|ошибка    |"err", "error"  |

## <a name="next-steps"></a>Дальнейшие действия

- [Включение ведения журнала диагностики для приложений в Службе приложений Azure](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Просмотрите параметры ведения журнала безопасности и аудита в Azure](/azure/security/fundamentals/log-audit)
- [Узнайте о том, как работать с журналами платформы Azure](/azure/azure-monitor/platform/platform-logs-overview)
