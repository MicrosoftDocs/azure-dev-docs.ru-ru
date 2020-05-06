---
title: Пакеты JDK и долгосрочная поддержка разработки в Azure
description: Файлы для скачивания и заявление о поддержке разработки и выполнения приложений на Java в Azure.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019
ms.openlocfilehash: 02915383dd72a18959dbb5ba0f925a0e10b691d7
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81670650"
---
# <a name="java-long-term-support-for-azure-and-azure-stack"></a>Долгосрочная поддержка Java для Azure и Azure Stack

В Azure и Azure Stack разработчики Java могут создавать и запускать производственные приложения Java с помощью сборок JDK [корпоративного выпуска Azul Zulu для Azure](https://www.azul.com/downloads/azure-only/zulu/) без дополнительных затрат на поддержку. В Azure можно задействовать любые среды выполнения Java, но при использовании Zulu вы получаете бесплатное обслуживание и можете направлять запросы в службу поддержки Майкрософт.

> [!div class="nextstepaction"]
> [Скачать и установить Java](java-jdk-install.md)

## <a name="long-term-support-lts"></a>Долгосрочная поддержка (LTS):

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts);
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts)

## <a name="technical-preview"></a>Техническая предварительная версия

* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>Что такое Zulu OpenJDK для Azure?

Корпоративные выпуски сборок типа Azul Zulu для Azure — это бесплатный мультиплатформенный готовый к использованию дистрибутив OpenJDK для Azure и Azure Stack, который поддерживается корпорацией Майкрософт и Azul Systems. Эти дистрибутивы:

* Являются сборками OpenJDK с полностью открытым кодом, которые упакованы в виде комплектов Java Development Kit (JDK), сред выполнения Java (JRE) и автономных сред JRE. Эти двоичные файлы являются полностью совместимыми коммерческими сборками Java Standard Edition (SE), которые можно использовать с приложениями или компонентами Java в Azure и Azure Stack.
* Предоставляются с долгосрочной поддержкой, включающей исправления ошибок, улучшения производительности и исправления системы безопасности.
* Доступны для разработки и запуска приложений Java на Windows, Linux и MacOS.
* Доступны как образы контейнеров в Docker Hub, а также как виртуальные машины (Windows и Linux) в Azure Marketplace.
* Используются Microsoft Azure для работы со многими службами Azure, такими как:
  * Служба приложений (Windows);
  * Служба приложений для Linux;
  * Функции
  * Service Fabric
  * HDInsight
  * Поиск
  * Azure DevOps
  * Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Поддерживаемые версии Java и график поддержки

Azul Systems предоставляет полностью поддерживаемые сборки [корпоративного выпуска Azul Zulu для Azure](https://www.azul.com/downloads/azure-only/zulu/) для всех версий Java с долгосрочной поддержкой, начиная с Java SE 7, 8 и 11. Дополнительные сведения см. в [пресс-релизе Azul](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack).

|Долгосрочная поддержка Java SE  |Поддержка до  |
|---------|----------|
|[![Поддерживаемая версия Java — Java 7](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |Июль 2023 г. |
|[![Поддерживаемая версия Java — Java 8](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |Март 2025 г.|
|[![Поддерживаемая версия Java — Java 11](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |Сентябрь 2026 г.|
|[![Поддерживаемая версия Java — Java 13](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |**Предварительная версия**|

Для этих выпусков JDK выходят ежеквартальные обновления для системы безопасности и исправления ошибок, а также критические внеплановые обновления и исправления при необходимости.  Поддержка включает в себя обратное портирование обновлений для системы безопасности и исправлений ошибок для Java 7 и 8, обнаруженных в более новых версиях Java, например Java 11, что гарантирует стабильную работу и безопасность приложений на старых версиях Java.  Пользователи Azure могут получить эти обновления для системы безопасности и исправления ошибок платформы, не неся при этом незапланированных расходов на подписку на Java SE.

Выпуски от Azul Systems учитывают [график выхода новых версий Java SE](https://www.azul.com/products/azul_support_roadmap/).

## <a name="benefits-for-developers"></a>Преимущества для разработчиков

Версии JDK корпоративного выпуска Azul Zulu для Azure:

1. Поддерживаются корпорацией Майкрософт и Azul Systems.

   * Двоичные файлы Zulu готовы к выпуску и поддерживаются корпорацией Майкрософт и Azul Systems.
   * Zulu поставляется с бесплатной долгосрочной поддержкой (LTS) для Java 7, 8, и 11. (LTS будет также предоставляться для Java 17.) Вы можете обновлять версии Java, только если это необходимо.
   * Java 7 поддерживается до июля 2023 г. Java 8 и 11 поддерживаются до 2024 года и дольше.
   * Корпорация Майкрософт стремится запустить Zulu внутри на компьютерах, которые поддерживают многие службы Azure.

2. Готовы к выпуску.

   * С полностью открытым кодом для сборок OpenJDK.
   * Упрощенные замены для многих дистрибутивов Java SE.
   * JDK, JRE и автономная JRE.
   * Java 7, 8 и 11.
   * Проверены на соответствие спецификациям Java SE с использованием комплекта технологической совместимости (TCK) сообщества OpenJDK.
   * Разработчики продолжат получать производственные обновления для Java SE, включая исправления ошибок, улучшения производительности и исправления системы безопасности для Java SE 7, 8 и 11.

3. Поддерживаются для нескольких платформ. Zulu поддерживает двоичные файлы для нескольких платформ и версий, в том числе:

   * Клиент Windows
     * 10
     * 8.1
     * 8, 7
   * Windows Server
     * 2016R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux, включая
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * Предоставляются в нескольких типах упаковки:
     * MSI, ZIP, TAR, DEB, RPM и DMG.

    В Docker доступны сертифицированные образы контейнеров Docker для Zulu JDK, JRE и автономной среды JRE для нескольких базовых образов ОС.

    Концентратор:

    * [JDK](https://hub.docker.com/_/microsoft-java-jdk).
    * [JRE](https://hub.docker.com/_/microsoft-java-jre)
    * [Автономная среда JRE](https://hub.docker.com/_/microsoft-java-jre-headless).

4. Бесплатны.

   * Корпорация Майкрософт предоставляет все необходимое для создания и масштабирования приложений Java в Azure бесплатно. Через Zulu вы будете получать бесплатные обновления системы безопасности и исправления ошибок платформы для приложений Java без какой-либо платы.
   * [Java Flight Recorder и Mission Control](java-jdk-flight-recorder-and-mission-control.md) доступны в Zulu Java 8, 11 и 12 (предварительная версия).

5. Технические предварительные версии без LTS

   * Технические предварительные версии позволяют постепенно протестировать новые функции, так как они предоставляются в краткосрочных версиях, которые в конечном итоге перейдут на долгосрочную поддержку Java 17.

6. Изменения в OpenJDK вносятся в вышестоящий источник.

   * Разработчики Azul Systems отправляют изменения Zulu в OpenJDK, что делает вышестоящий репозиторий целостным и инклюзивным.

Как всегда, разработчики Java могут перенести в Azure свои собственные среды выполнения Java, включая Oracle JDK и Red Hat JDK, и использовать защищенную инфраструктуру и многофункциональные службы. Выпуск Oracle Java SE для рабочей среды также доступен для разработчиков Java, выполняющих рабочие нагрузки Java на виртуальных машинах Azure (Windows или Linux).

## <a name="use-for-local-development"></a>Использование в локальной разработке

Пакеты Java JDK для Azure и Azure Stack доступны для [скачивания](https://www.azul.com/downloads/azure-only/zulu/) разработчикам для использования в локальных средах разработки. Файлы для скачивания доступны для Windows, Linux и macOS. Разработчики, работающие на платформе Linux, могут также получить пакеты с помощью диспетчеров пакетов [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) и [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo).

Дополнительные рекомендации см. в статье [Use Docker with a JDK for Azure](java-jdk-docker-images.md) (Использование Docker с JDK для Azure).