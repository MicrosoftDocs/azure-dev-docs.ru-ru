---
title: Создание журналов с помощью Azure SDK для Java и java.util.logging
description: Обзор интеграции Azure SDK для Java с java.util.logging
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 48fa4dac679e8b39139e03ae65f331072a063710
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528653"
---
# <a name="log-with-the-azure-sdk-for-java-and-javautillogging"></a>Создание журналов с помощью Azure SDK для Java и java.util.logging

В этой статье приводятся общие сведения о добавлении ведения журналов в приложения, использующие Azure SDK для Java, с помощью java.util.logging. Платформа java.util.logging является частью JDK. Как упоминалось в статье [Настройка ведения журнала в Azure SDK для Java](logging-overview.md), все клиентские библиотеки Azure ведут журналы с использованием [SLF4J](http://www.slf4j.org/), поэтому вы можете использовать такие платформы ведения журналов, как [java.util.logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/Logger.html).

Чтобы включить ведение журнала с помощью java.util.logging, необходимо выполнить два действия:

1. Настроить адаптер SLF4J для java.util.logging в качестве зависимости.
2. Создать файл с именем *logging.properties* в каталоге проекта */src/main/resources*.

Дополнительные сведения о настройке средства ведения журнала см. в статье [Настройка выходных данных ведения журнала](https://docs.oracle.com/cd/E23549_01/doc.1111/e14568/handler.htm) документации Oracle.

## <a name="add-the-maven-dependency"></a>Добавление зависимости Maven

Чтобы добавить зависимость Maven, включите следующий код XML в файл проекта *pom.xml*. Замените номер версии *1.7.30* номером последней выпущенной версии, показанной на [странице привязки SLF4J JDK14](https://mvnrepository.com/artifact/org.slf4j/slf4j-jdk14).

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>1.7.30</version> <!-- replace this version with the latest available version on Maven central -->
</dependency>
```

## <a name="add-loggingproperties-to-your-project"></a>Добавление файла logging.properties в проект

Чтобы создавать журналы с помощью `java.util.logging`, создайте файл с именем *logging.properties* в каталоге проекта *./src/main/resources*. Этот файл будет содержать конфигурации ведения журналов для настройки ведения журнала. Дополнительные сведения см. в разделе [Ведение журнала Java. Конфигурация](http://tutorials.jenkov.com/java-logging/configuration.html).

Если вы хотите использовать имя файла, отличное от *logging.properties*, это можно сделать, задав системное свойство `java.util.logging.config.file`. Это свойство необходимо задавать до создания экземпляра средства ведения журнала.

### <a name="console-logging"></a>Журнал консоли

Вы можете создать конфигурацию для создания журналов в консоли, как показано в следующем примере. В этом примере конфигурация приведет к регистрации всех событий ведения журнала уровня INFO или более высокого уровня, независимо от того, что является источником таких событий.

```properties
handlers = java.util.logging.ConsoleHandler
.level = INFO

java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.SimpleFormatter.format=[%1$tF %1$tT] [%4$s] %5$s %n
```

### <a name="log-to-a-file"></a>Создание журнала в файле

В приведенном выше примере журнал создается в консоли, что обычно не является предпочтительным расположением для журналов. Чтобы настроить ведение журнала в файле, используйте следующую конфигурацию:

```properties
handlers = java.util.logging.FileHandler
.level = INFO

java.util.logging.FileHandler.pattern = %h/myapplication.log
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.level = INFO
```

Этот код создаст файл с именем *myapplication.log* в домашнем каталоге (`%h`). Это средство ведения журнала не поддерживает автоматическую смену файлов через определенное время. Если вам нужна эта функция, напишите планировщик заданий для управления сменой файла журнала.

## <a name="next-steps"></a>Следующие шаги

В этой статье описано, как выполнить конфигурацию `java.util.logging` и о том, как настроить Azure SDK для Java, чтобы обеспечить использование такой конфигурации для ведения журнала этим средством. Поскольку Azure SDK для Java работает со всеми платформами ведения журналов SLF4J, ознакомьтесь с [руководством пользователя SLF4J](http://www.slf4j.org/manual.html), чтобы получить дополнительные сведения.

После того как вы научитесь вести журнал, рассмотрите возможность изучения интеграций, предоставляемых Azure для таких платформ, как [Spring](/azure/developer/java/spring-framework/spring-boot-starters-for-azure) и [MicroProfile](/azure/developer/java/eclipse-microprofile/).
