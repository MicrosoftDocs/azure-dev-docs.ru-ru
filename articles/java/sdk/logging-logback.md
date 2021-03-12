---
title: Создание журналов с помощью Azure SDK для Java и Logback
description: Обзор интеграции Azure SDK для Java с logback
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 785bb65a1a6f55314246d4c1410891717f8bbd6f
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118162"
---
# <a name="log-with-the-azure-sdk-for-java-and-logback"></a>Создание журналов с помощью Azure SDK для Java и Logback

В этой статье приводятся общие сведения о добавлении ведения журналов в приложения, использующие Azure SDK для Java, с помощью Logback. Как упоминалось в статье [Настройка ведения журнала в Azure SDK для Java](logging-overview.md), все клиентские библиотеки Azure ведут журналы с использованием [SLF4J](http://www.slf4j.org/), поэтому вы можете использовать такие платформы ведения журналов, как [Logback](http://logback.qos.ch/).

Чтобы включить ведение журнала с помощью Logback, необходимо выполнить два действия:

1. Настроить библиотеку Logback в качестве зависимости.
2. Создать файл с именем *logback.xml* в каталоге проекта */src/main/resources*.

Дополнительные сведения о настройке Logback см. в разделе [Настройка Logback](http://logback.qos.ch/manual/configuration.html) документации по Logback.

## <a name="add-the-maven-dependency"></a>Добавление зависимости Maven

Чтобы добавить зависимость Maven, включите следующий код XML в файл проекта *pom.xml*. Замените номер версии *1.2.3* номером последней выпущенной версии, показанной на [странице классического модуля Logback](https://mvnrepository.com/artifact/ch.qos.logback/logback-classic).

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

## <a name="add-logbackxml-to-your-project"></a>Добавление файла logback.xml в проект

[Logback](https://logback.qos.ch/manual/introduction.html) — одна из популярных платформ ведения журналов. Чтобы включить ведение журнала с помощью Logback, создайте файл с именем *logback.xml* в каталоге проекта *./src/main/resources*. Этот файл будет содержать конфигурации ведения журналов для настройки ведения журнала. Дополнительные сведения о настройке *logback.xml* см. в разделе [Настройка Logback](https://logback.qos.ch/manual/configuration.html) документации по Logback.

### <a name="console-logging"></a>Журнал консоли

Вы можете создать конфигурацию Logback для создания журналов в консоли, как показано в следующем примере. В этом примере конфигурация приведет к регистрации всех событий ведения журнала уровня INFO или более высокого уровня, независимо от того, что является источником таких событий.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

### <a name="log-azure-core-errors"></a>Создание журналов об основных ошибках Azure

Приведенный ниже пример конфигурации аналогичен предыдущей конфигурации, однако в этом примере уровень, при котором ведение журнала поступает из всех упакованных классов `com.azure.core` (включая подпакеты), снижается. Таким образом в журнал будут записаны все данные уровня INFO и более высоких уровней, за исключением `com.azure.core`, при котором в журнале будут регистрироваться только данные уровня ERROR и более высоких уровней. Этот подход можно использовать, например, если вы считаете код в `com.azure.core` ошибочным. Такой тип конфигурации также можно использовать двумя способами. Например, если вам нужно получить дополнительные сведения об отладке из классов в `com.azure.core`, этот параметр можно заменить на параметр DEBUG.

Вы сможете тщательно контролировать ведение журнала для конкретных классов или отдельных пакетов. Как показано ниже, `com.azure.core` будет управлять выходными данными всех основных классов, однако вы можете в равной степени использовать `com.azure.security.keyvault` или его эквивалент, чтобы управлять выходными данными в соответствии с обстоятельствами, которые являются наиболее информативными в контексте выполняющегося приложения.

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

### <a name="log-to-a-file-with-log-rotation-enabled"></a>Создание журналов в файле с поддержкой смены журнала

В предыдущем примере журнал создается в консоли, что обычно не является предпочтительным расположением для журналов. Используйте приведенную ниже конфигурацию, чтобы записывать данные в файл, а также для почасовой смены и архивации в GZIP-формате:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="INFO">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="spring-applications"></a>Приложения Spring

В основе работы платформы Spring лежит считывание данных файла *application.properties* Spring для различных конфигураций, включая конфигурацию ведения журнала. Приложение Spring также можно настроить, чтобы оно считывало конфигурации Logback из любого файла. Для этого настройте свойство `logging.config` таким образом, чтобы оно указывало на файл конфигурации *logback.xml*, добавив в файл Spring */src/main/resources/application.properties* следующую строку:

```properties
logging.config=classpath:logback.xml
```

## <a name="next-steps"></a>Следующие шаги

В этой статье описано, как выполнить конфигурацию Logback и о том, как заставить Azure SDK для Java использовать его для ведения журнала. Поскольку Azure SDK для Java работает со всеми платформами ведения журналов SLF4J, ознакомьтесь с [руководством пользователя SLF4J](http://www.slf4j.org/manual.html), чтобы получить дополнительные сведения. Если вы используете Logback, на веб-сайте этого средства также можно найти большое количество руководств по настройке. Дополнительные сведения см. в разделе[Настройка Logback](http://logback.qos.ch/manual/configuration.html) документации по Logback.

После того как вы научитесь вести журнал, рассмотрите возможность изучения интеграций, предоставляемых Azure для таких платформ, как [Spring](../spring-framework/spring-boot-starters-for-azure.md) и [MicroProfile](../eclipse-microprofile/index.yml).