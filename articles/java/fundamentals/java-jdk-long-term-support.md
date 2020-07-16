---
title: Пакеты JDK, а также среднесрочная и долгосрочная поддержка разработки в Azure
description: В этой статье содержатся ссылки для скачивания и заявление о поддержке Azure для разработки и запуска приложений Java.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019, devx-track-java
ms.openlocfilehash: 5bffb4e4d2f68ef61ea96ededdf51ea98bb72d2a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379838"
---
# <a name="java-long-term-support-and-medium-term-support-on-azure-and-azure-stack"></a>Средне- и долгосрочная поддержка Java в Azure и Azure Stack

В Azure и Azure Stack разработчики Java могут создавать и запускать производственные приложения Java с помощью сборок JDK [корпоративного выпуска Azul Zulu для Azure](https://www.azul.com/downloads/azure-only/zulu/) без дополнительных затрат на поддержку. Вы можете использовать любую среду выполнения Java в Azure. Но при работе с Zulu вы получаете бесплатные обновления для обслуживания и можете создавать запросы в службу поддержки Майкрософт.

Выпуски, отмеченные как LTS (долгосрочная поддержка), — это те же самые выпуски LTS, отмеченные компанией Oracle и сообществом OpenJDK. Для выпусков LTS мы предоставляем доступ к исправлениям ошибок, обновлениям безопасности и другим исправлениям (производственная поддержка) в течение минимум 8 лет по мере необходимости. Мы также предоставляем дополнительную поддержку для тех пользователей, которые переходят на более новую версию JDK (расширенная поддержка), в течение 2 лет.

Для выпусков, отмеченных как MTS (среднесрочная поддержка), мы предоставляем производственную поддержку в течение минимум 1,5 лет после выхода общедоступной версии следующего выпуска LTS. Также предоставляется дополнительная расширенная поддержка в течение 1 года.

> [!div class="nextstepaction"]
> [Скачать и установить Java](java-jdk-install.md)

## <a name="long-term-support-lts"></a>Долгосрочная поддержка (LTS):

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts);
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts)

## <a name="medium-term-support-mts"></a>Среднесрочная поддержка (MTS)

* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13)

## <a name="technical-preview"></a>Техническая предварительная версия

* [Java 14](https://www.azul.com/downloads/azure-only/zulu/?version=java-14)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>Что такое Zulu OpenJDK для Azure?

Корпоративные выпуски сборок Azul Zulu для Azure — это бесплатный мультиплатформенный, готовый к использованию дистрибутив OpenJDK для Azure и Azure Stack. Они поддерживаются корпорацией Майкрософт и компанией Azul Systems. Эти дистрибутивы:

* Являются сборками OpenJDK с полностью открытым кодом, которые упакованы в виде комплектов Java Development Kit (JDK), сред выполнения Java (JRE) и автономных сред JRE. Эти двоичные файлы являются полностью совместимыми коммерческими сборками Java Standard Edition (SE), которые можно использовать с приложениями или компонентами Java в Azure и Azure Stack.
* Для них предоставляется долгосрочная поддержка, включающая исправления ошибок, а также улучшения производительности и исправления системы безопасности.
* Доступны для разработки и запуска приложений Java на Windows, Linux и macOS.
* Доступны как образы контейнеров в Docker Hub, а также как виртуальные машины (Windows и Linux) в Azure Marketplace.
* Используются Microsoft Azure для работы со многими службами Azure, такими как:
  * Служба приложений в Windows
  * Служба приложений в Linux
  * Функции Azure
  * Azure Service Fabric
  * Azure HDInsight
  * Когнитивный поиск Azure
  * Azure DevOps
  * Azure Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Поддерживаемые версии Java и график поддержки

Azul Systems предоставляет полностью поддерживаемые сборки [корпоративного выпуска Azul Zulu для Azure](https://www.azul.com/downloads/azure-only/zulu/) для всех версий Java с долгосрочной и среднесрочной поддержкой, в том числе Java SE 7, 8, 11 и 13. Дополнительные сведения см. в [пресс-релизе по Azul](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack) и плане [жизненного цикла поддержки продуктов Azul](https://www.azul.com/products/azul_support_roadmap/).

|Версия Java SE  |Поддерживается до  |
|---------|----------|
|[![Логотип Java 7](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |Июль 2023 г. (LTS)|
|[![Логотип Java 8](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |Декабрь 2030 г. (LTS)|
|[![Логотип Java 11](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |Сентябрь 2027 г. (LTS)|
|[![Логотип Java 13](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |Март 2023 г. (MTS)|
|[![Логотип Java 14](media/supported-java-versions-java-14.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-14) |**Предварительный просмотр**|

Для LTS- и MTS-выпусков JDK выходят ежеквартальные обновления системы безопасности и исправления ошибок, а также критические внеплановые обновления и исправления по мере необходимости. Эта поддержка предусматривает обратный перенос в Java 7 и 8 обновлений безопасности и исправления ошибок, обнаруженных в более новых версиях Java, как, например, Java 11. Обратный перенос гарантирует стабильность и безопасность более старых версий Java. Пользователи Azure могут получить эти обновления для системы безопасности и исправления ошибок платформы, не неся при этом незапланированных расходов на подписку на Java SE.

В настоящее время для Функций Azure требуется Java 8, а поддержка Java 11 находится в стадии разработки.

## <a name="benefits-for-developers"></a>Преимущества для разработчиков

Версии JDK корпоративного выпуска Azul Zulu для Azure имеют следующие преимущества:

- Поддерживаются корпорацией Майкрософт и Azul Systems.

   * Двоичные файлы Zulu готовы к выпуску и поддерживаются корпорацией Майкрософт и Azul Systems.
   * Zulu поставляется с бесплатной долгосрочной поддержкой (LTS) для Java 7, 8 и 11 и среднесрочной поддержкой (MTS) для Java 13. (LTS будет также предоставляться для Java 17.) Вы можете обновлять версии Java, только если это необходимо.
   * Java 7 поддерживается до июля 2023 г. Java 8 поддерживается до декабря 2030 г. Java 11 поддерживается до сентября 2027 г. Java 13 получает поддержку до марта 2023 г.
   * Корпорация Майкрософт стремится запустить Zulu внутри на компьютерах, которые поддерживают многие службы Azure.

- Готовы к производству.

   * С полностью открытым кодом для сборок OpenJDK.
   * Упрощенные замены для многих дистрибутивов Java SE.
   * JDK, JRE и автономная JRE.
   * Java 7, 8, 11 и 13.
   * Проверены на соответствие спецификациям Java SE с помощью комплекта технологической совместимости (TCK) сообщества OpenJDK.
   * Включают производственные обновления для Java SE, включая исправления ошибок, улучшения производительности и исправления системы безопасности для Java SE 7, 8, 11 и 13.

- Поддерживаются для многоплатформенных систем. Zulu поддерживает двоичные файлы для разных платформ и версий, в том числе:

   * Windows
     * 10
     * 8.1
     * 8
     * 7
   * Windows Server
     * 2016 R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * Предоставляются в нескольких типах упаковки:
     * MSI, ZIP, TAR, DEB, RPM и DMG.

     В Docker Hub доступны сертифицированные образы контейнеров Docker для Zulu JDK, JRE и автономной среды JRE на основе нескольких базовых образов ОС:

     * [JDK](https://hub.docker.com/_/microsoft-java-jdk).
     * [JRE](https://hub.docker.com/_/microsoft-java-jre)
     * [Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)

- Являются бесплатными.

   * Корпорация Майкрософт бесплатно предоставляет все необходимое для создания и масштабирования приложений Java в Azure. Через Zulu вы будете получать бесплатные обновления системы безопасности и исправления ошибок платформы для приложений Java.
   * [Java Flight Recorder и Zulu Mission Control](java-jdk-flight-recorder-and-mission-control.md) доступны в Zulu Java 8, 11 и последующих версиях.

- Включают технические предварительные версии других версий, кроме LTS и MTS.

   * Технические предварительные версии позволяют протестировать новые возможности сразу после их внедрения в текущих версиях, которые со временем будут перенесены в Java 17 LTS.

- Включают вышестоящие изменения в OpenJDK.
   * Разработчики Azul Systems при фиксации передают внесенные в Zulu изменения в OpenJDK. Такие фиксации делают вышестоящей репозиторий более полным и инклюзивным.

При этом разработчики Java могут перенести в Azure любые среды выполнения Java, включая Oracle JDK и Red Hat JDK, и применять для них защищенную инфраструктуру и многофункциональные службы. Выпуск Oracle Java SE для рабочей среды также доступен для разработчиков Java, выполняющих рабочие нагрузки Java на виртуальных машинах Azure (Windows или Linux).

## <a name="use-for-local-development"></a>Использование в локальной разработке

Пакеты Java JDK для Azure и Azure Stack доступны для [скачивания](https://www.azul.com/downloads/azure-only/zulu/) разработчикам для использования в локальных средах разработки. Файлы для скачивания доступны для Windows, Linux и macOS. Разработчики, работающие на платформе Linux, могут также получить пакеты с помощью диспетчеров пакетов [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) и [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo).

Дополнительные сведения см. в статье [Использование Docker с JDK (Java Development Kit) в Azure](java-jdk-docker-images.md).
