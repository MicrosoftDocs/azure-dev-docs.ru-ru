---
title: Просмотр данных с помощью Java Flight Recorder и Mission Control
description: Руководство по использованию инструмента Java Flight Recorder и сборки Mission Control для сбора и просмотра данных приложения.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 14084d2fb89c8adc78a0455b8bdd2cc0b5b6ec4c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812247"
---
# <a name="monitor-and-manage-java-workloads-with-java-flight-recorder-jfr-and-zulu-mission-control"></a>Мониторинг рабочих нагрузок Java и управление ими с помощью Java Flight Recorder (JFR) и Zulu Mission Control

Узнайте, как выполнять мониторинг и администрирование рабочих нагрузок Java с помощью Java Flight Recorder (JFR) и Zulu Mission Control.

Zulu Mission Control — это полностью проверенная сборка JDK Mission Control, представленная с открытым кодом компанией Oracle в 2018 году. Управление сборкой осуществляется как управление проектом на базе OpenJDK. Flight Recorder и Mission Control вместе предоставляют интерактивные возможности управления и мониторинга с минимальными затратами для рабочих нагрузок Java.

Сборка Zulu Mission Control совместима со следующими пакетами JDK и средами JRE:

* Zulu 12.1 и более поздние версии;
* Zulu 11.0 и более поздние версии;
* Zulu 8u202 (8.36) и более поздние версии;
* Oracle OpenJDK 11+15 и более поздние версии;
* Oracle Java 11.0 и более поздние версии;
* Oracle Java 8.0 и более поздние версии.

Выполните приведенные ниже действия, чтобы установить Zulu Mission Control, подключиться к виртуальной машине Java (JVM) и получать представление обо всех аспектах запущенного приложения в режиме реального времени.

1.  [Установите экземпляр JDK/JRE, совместимый с Zulu Mission Control](java-jdk-install.md).

2.  Скачайте Zulu Mission Control из [сайта загрузки Azul](https://www.azul.com/products/zulu-mission-control/), выберите необходимую версию для системы, сохраните ее в локальном расположении и перейдите к этому каталогу.

3.  Разверните скачанный файл.

    **Linux:**

    ```cli
    tar -xzvf zmc7.0.0-EA-linux_x64.tar.gz
    ```

    **Windows:**

    ```cli
    unzip -zxvf zmc7.0.0-EA-win_x64.zip 
    ```

    **MacOS:**

    ```cli
    tar -xzvf zmc7.0.0-EA-macosx_x64.tar.gz
    ```

4.  Запустите приложение Java с использованием одного из совместимых пакетов JDK. Например:

    ```cli
    $JAVA_HOME/bin/java -jar MyApplication.jar
    ```

5.  Запустите Zulu Mission Control.

    **Linux:**

    ```cli
    zmc7.0.0-EA-linux_x64/zmc
    ```

    **Windows:**

    ```cli
    zmc7.0.0-EA-win_x64\zmc.exe 
    ```

    **MacOS:**

    ```cli
    zmc7.0.0-EA-macosx_x64/Zulu\ Mission\ Control.app/Contents/MacOS/zmc
    ```

6.  Измените установку JVM для Mission Control (необязательно).

    В Windows *zmc.exe* будет использовать установку JVM по умолчанию, настроенную в реестре. Zulu Mission Control необходимо запустить из полного JDK, чтобы иметь возможность автоматически обнаруживать локальные экземпляры JVM. Если это среда JRE, вы увидите следующее предупреждение:

    > [!div class="mx-imgBorder"]
    ![Предупреждение, если установка JDK является только JRE](../media/jdk/jfr-jre-warning-message.png)

    Чтобы изменить JVM, используемую сборкой Mission Control, выполните следующие действия. 
    1.  Откройте файл конфигурации *zmc.ini*, расположенный в том же каталоге, что и *zmc.exe*.
    2.  Добавьте две строки перед строкой `-vmargs`:
        * В первой строке напишите `–vm`.
        * Во второй строке напишите путь к установке JDK. (Например, `C:\Program Files\Java\jdk1.8.0_212\bin\javaw.exe`).

7.  Найдите JVM, на которой запущено приложение.
    1.  На верхней левой панели окна Zulu Mission Control щелкните вкладку **JVM Browser** (Браузер JVM).
    2.  Выберите и разверните элемент списка в левом верхнем углу для экземпляра JVM, на котором запущено приложение.

    > [!div class="mx-imgBorder"]
    ![Развертывание элемента списка в левом верхнем углу для экземпляра JVM](../media/jdk/jfr-jvm-instance-dashboard.png)


8.  При необходимости начните запись с помощью инструмента Flight Recorder.
    1.  Если в Flight Recorder отображается No Recordings (Нет записей), начните запись. Для этого щелкните строку Flight Recorder правой кнопкой мыши на вкладке JVM Browser (Браузер JVM) и выберите **Start Flight Recording...** (Начать запись во время выполнения...).
    2.  Выберите запись с фиксированной продолжительностью или непрерывную запись, а также конфигурацию профилирования (точную) или непрерывную конфигурацию (меньшие общие расходы), а затем нажмите кнопку **Finish** (Готово).

    > [!div class="mx-imgBorder"]
    ![Начало записи во время выполнения приложения](../media/jdk/jfr-start-flight-recording.png)

9.  Создайте дамп записи, осуществляемой во время выполнения приложения.
    1.  Строка записи, осуществляемой во время выполнения приложения, должна отобразиться под строкой Flight Recorder на вкладке JVM Browser (Браузер JVM). Щелкните правой кнопкой мыши строку с записью, осуществляемой во время выполнения приложения, и выберите **Dump whole recording** (Создать дамп всей записи).
    2.  Отобразится новая вкладка в большой области справа от окна Zulu Mission Control. Эта область представляет запись, осуществляемую во время выполнения приложения, дамп которой создан из JVM, на которой запущено приложение.

10. Проверьте запись, осуществляемую во время выполнения приложения, используя Zulu Mission Control.
    1.  Если активация еще не выполнена, выберите вкладку **Outline** (Структура) в левой области окна Zulu Mission Control. Эта вкладка содержит различные представления данных, собранных в рамках записи, осуществляемой во время выполнения приложения.
 
    > [!div class="mx-imgBorder"]
    ![Просмотр записи, сделанной во время выполнения приложения](../media/jdk/jfr-zulu-mission-control-data.png)

## <a name="resources"></a>Ресурсы

Кроме того, мы приготовили [демонстрационное видео](https://www.azul.com/presentation/azul-webinar-open-source-flight-recorder-and-mission-control-managing-and-measuring-openjdk-8-performance/), комментируемое техническим директором Azul Systems Саймоном Риттером (Simon Ritter). В этом видео описывается настройка и установка Flight Recorder и Zulu Mission Control. Обсуждение Flight Recorder начинается на отметке времени 31:30.

