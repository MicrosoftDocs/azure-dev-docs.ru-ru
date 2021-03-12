---
title: Создание журналов с помощью Azure SDK для Java и Log4j
description: Обзор интеграции Azure SDK для Java с log4j
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 0de76274b7f33f724c339eb0137a89d74f3e8678
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118476"
---
# <a name="log-with-the-azure-sdk-for-java-and-log4j"></a>Создание журналов с помощью Azure SDK для Java и Log4j

В этой статье приводятся общие сведения о добавлении ведения журналов в приложения, использующие Azure SDK для Java, с помощью Log4j. Как упоминалось в статье [Настройка ведения журнала в Azure SDK для Java](logging-overview.md), все клиентские библиотеки Azure ведут журналы с использованием [SLF4J](http://www.slf4j.org/), поэтому вы можете использовать такие платформы ведения журналов, как [log4j](https://logging.apache.org/log4j/2.x/).

В этой статье представлено руководство по использованию Log4J 2.x, однако Azure SDK для Java также поддерживает Log4J 1.x. Чтобы включить ведение журнала с помощью log4j, необходимо выполнить два действия:

1. Настроить библиотеку log4j в качестве зависимости.
2. Создать файл конфигурации (либо *log4j2.properties*, либо *log4j2.xml*) в каталоге проекта */src/main/resources*.

Дополнительные сведения о настройке log4j см. в статье [Вас приветствует Log4j 2](https://logging.apache.org/log4j/2.x/manual/index.html).

## <a name="add-the-maven-dependency"></a>Добавление зависимости Maven

Чтобы добавить зависимость Maven, включите следующий код XML в файл проекта *pom.xml*. Замените номер версии *2.14.0* номером последней выпущенной версии, показанной на [странице привязки Apache Log4j SLF4J](https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl).

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.14.0</version>
</dependency>
```

## <a name="configuring-log4j"></a>Настройка Log4j

Существует два стандартных способа настройки Log4j: через внешний файл свойств или с помощью внешнего XML-файла. Эти подходы описаны ниже.

### <a name="using-a-property-file"></a>Использование файла свойств

Вы можете разместить неструктурированный файл свойств с именем *log4j2.properties* в каталог проекта */src/main/resource*. Этот файл должен иметь следующий вид:

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n

logger.app.name = com.azure.core
logger.app.level = ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

### <a name="using-an-xml-file"></a>Использование XML-файла

Вы можете разместить XML-файл с именем *log4j2.xml* в каталог проекта */src/main/resource*. Этот файл должен иметь следующий вид:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%msg%n" />
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

## <a name="next-steps"></a>Следующие шаги

В этой статье описано, как выполнить конфигурацию Log4j и о том, как настроить Azure SDK для Java, чтобы обеспечить использование такой конфигурации для ведения журнала этим средством. Поскольку Azure SDK для Java работает со всеми платформами ведения журналов SLF4J, ознакомьтесь с [руководством пользователя SLF4J](http://www.slf4j.org/manual.html), чтобы получить дополнительные сведения. Если вы используете Log4j, на веб-сайте этого средства также можно найти большое количество руководств по настройке. Дополнительные сведения см. в статье [Вас приветствует Log4j 2!](https://logging.apache.org/log4j/2.x/manual/index.html).

После того как вы научитесь вести журнал, рассмотрите возможность изучения интеграций, предоставляемых Azure для таких платформ, как [Spring](../spring-framework/spring-boot-starters-for-azure.md) и [MicroProfile](../eclipse-microprofile/index.yml).