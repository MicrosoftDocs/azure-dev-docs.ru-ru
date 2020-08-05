---
title: Настройка ведения журнала с пакетом Azure SDK для Java
description: Узнайте, как настроить платформы ведения журналов для клиентских библиотек Java в Azure SDK
keywords: Azure, Java, SDK, ведение журнала
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.custom: devx-track-java
ms.openlocfilehash: 5bb7f711eae230a08893d2f94c242a06af809f88
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400622"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Настройка ведения журнала с пакетом Azure SDK для Java

В этой статье приводятся примеры настройки ведения журналов для [пакета SDK для Azure](https://azure.microsoft.com/downloads/) для Java. Дополнительные сведения о параметрах конфигурации, таких как настройка уровней ведения журнала или пользовательское ведение журналов по классам, см. в документации выбранной платформы ведения журналов.

Пакет Azure SDK для клиентских библиотек Java использует [Simple Logging Facade for Java](https://www.slf4j.org/) (SLF4J). SLF4J позволяет использовать предпочтительную платформу ведения журналов, которая вызывается во время развертывания приложения.

> [!NOTE]
> Эта статья применима к последним версиям клиентских библиотек пакета Azure SDK. Чтобы узнать, поддерживается ли библиотека, обратитесь к списку [последних выпусков Azure SDK](https://azure.github.io/azure-sdk/releases/latest/java.html). Если приложение использует более раннюю версию клиентских библиотек пакета Azure SDK, ознакомьтесь с конкретными инструкциями в соответствующей документации службы.

## <a name="declare-a-logging-framework"></a>Объявление платформы ведения журналов

Перед реализацией этих средств ведения журнала необходимо объявить соответствующую платформу в качестве зависимости в проекте. Дополнительные сведения см. в [руководстве по SLF4J для пользователей](https://www.slf4j.org/manual.html#projectDep).

В следующих разделах приведены примеры конфигурации для распространенных платформ ведения журналов.

## <a name="use-log4j"></a>Использование Log4j

В следующих примерах приведены конфигурации для платформы ведения журналов Log4j. Дополнительные сведения см. в [документации по Log4j](https://logging.apache.org/log4j/1.2/).

**Включение Log4j путем добавления зависимости Maven**

Добавьте следующий код в файл *pom.xml* проекта:

```xml
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>[1.0,)</version> <!-- Version number 1.0 and above -->
</dependency>
```

**Включение Log4j с помощью файла свойств**

Создайте файл *log4j.properties* в каталоге *./src/main/resource* проекта и добавьте следующее содержимое:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

**Включение Log4j с помощью файла XML**

Создайте файл *log4j.xml* в каталоге *./src/main/resource* проекта и добавьте следующее содержимое:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%m%n" />
        </layout>
    </appender>
    <logger name="com.azure.core">
        <level value="ERROR" />
        <appender-ref ref="console" />
    </logger>

    <root>
        <level value="info" />
        <appender-ref ref="console" />
    </root>

</log4j:configuration>
```

## <a name="use-log4j-2"></a>Использование Log4j 2

В следующих примерах приведены конфигурации для платформы ведения журналов Log4j 2. Дополнительные сведения см. в [документации по Log4j 2](https://logging.apache.org/log4j/2.x/manual/configuration.html).

**Включение Log4j 2 путем добавления зависимости Maven**

Добавьте следующий код в файл *pom.xml* проекта:

```
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>[2.0,)</version> <!-- Version number 2.0 and above -->
</dependency>
```

**Включение Log4j 2 с помощью файла свойств**

Создайте файл *log4j2.properties* в каталоге *./src/main/resource* проекта и добавьте следующее содержимое:

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

**Включение Log4j 2 с помощью файла XML**

Создайте файл *log4j2.xml* в каталоге *./src/main/resource* проекта и добавьте следующее содержимое:

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

## <a name="use-logback"></a>Использование Logback

В следующих примерах приведены базовые конфигурации для платформы ведения журналов Logback. Дополнительные сведения см. в [документации по Logback](https://logback.qos.ch/manual/configuration.html).

**Включение Logback путем добавления зависимости Maven**

Добавьте следующий код в файл *pom.xml* проекта:

```
<!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>[0.2.5,)</version> <!-- Version number 0.2.5 and above -->
</dependency>
```

**Включение Logback с помощью файла XML**

Создайте файл *logback.xml* в каталоге *./src/main/resources* проекта и добавьте следующее содержимое:

```xml
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

## <a name="use-logback-in-a-spring-boot-application"></a>Использование Logback в приложении Spring Boot

В следующих примерах приведены некоторые конфигурации для использования Logback со Spring. Обычно конфигурации ведения журналов добавляются в файл *logback.xml* в каталоге *./src/main/resources* проекта. Spring выполняет поиск различных конфигураций в этом файле, в том числе для ведения журналов. Дополнительные сведения см. в [документации по Logback](https://logback.qos.ch/manual/configuration.html).

Вы можете настроить свое приложение на считывание конфигураций Logback из любого файла. Чтобы связать файл *logback.xml* с приложением Spring, создайте файл *application.properties* в каталоге *./src/main/resources* проекта и добавьте следующее содержимое:

```properties
logging.config=classpath:logback.xml
```

Чтобы создать конфигурацию Logback для ведения журнала в консоли, добавьте следующий код в файл *logback.xml*:

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

Чтобы настроить ведение журнала в файл, который перезаписывается каждый час и архивируется в формате GZIP, добавьте следующий код в файл *logback.xml*:

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

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Настройка резервного журнала для временной отладки

В сценариях, где невозможно повторно развернуть приложение с помощью средства ведения журналов SLF4J, можно использовать резервное средство ведения журналов, встроенное в клиентские библиотеки Azure для Java, в Azure Core 1.3.0 или более поздней версии. Чтобы включить это средство ведения журнала, необходимо сначала убедиться в отсутствии средства ведения журнала SLF4J (так как оно будет иметь приоритет), а затем задать переменную среды `AZURE_LOG_LEVEL`. После задания переменной среды, чтобы начать создание журналов необходимо перезапустить приложение.

В следующей таблице приведены допустимые значения для этой переменной среды.

|Уровень ведения журнала   |Разрешенные значения переменных среды   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATIONAL|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|ошибка    |"err", "error"  |

## <a name="next-steps"></a>Следующие шаги

- [Включение функции ведения журналов диагностики для приложений в Службе приложений Azure](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Просмотрите параметры ведения журнала безопасности и аудита в Azure](/azure/security/fundamentals/log-audit)
- [Узнайте о том, как работать с журналами платформы Azure](/azure/azure-monitor/platform/platform-logs-overview)
