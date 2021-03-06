---
title: Перенос приложений Java в Azure
description: Эта статья содержит общие сведения о рекомендуемых стратегиях переноса приложений Java в Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 005fc97633135f8458b0650b9a3afb49e841001d
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89061993"
---
# <a name="migrate-java-applications-to-azure"></a>Перенос приложений Java в Azure

Эта статья содержит общие сведения о рекомендуемых стратегиях переноса приложений Java в Azure.

В этом руководстве по миграции рассматриваются распространенные сценарии работы с Java в Azure, а также общие рекомендации и советы по планированию миграции. Если вы хотите обсудить конкретный сценарий миграции приложений Java со специалистами из Майкрософт по работе с Java в Azure, заполните следующую анкету и представитель свяжется с вами.

> [!div class="nextstepaction"]
> [Анкета с вопросами по миграции Java](https://aka.ms/migrate-my-Java-app-requested-thru-docs)

## <a name="identifying-application-type"></a>Определение типа приложения

Прежде чем выбрать для приложения Java место назначения в облаке, необходимо указать тип этого приложения. Большинство приложений Java относятся к одному из следующих типов:

* [приложения Spring Boot (JAR);](#spring-boot--jar-applications)
* [приложения Spring Cloud (микрослужбы);](#spring-cloud--microservices)
* [Веб-приложения](#web-applications)
* [приложения Java EE;](#java-ee-applications)
* [пакетные или запланированные задания.](#batch--scheduled-jobs)

Эти типы описаны в следующих разделах.

### <a name="spring-boot--jar-applications"></a>Приложения Spring Boot (JAR)

Многие новые приложения вызываются непосредственно из командной строки. Эти приложения по-прежнему обрабатывают веб-запросы, но вместо того чтобы ожидать обработки запросов HTTP от сервера приложений, они включают связь по HTTP и все остальные зависимости непосредственно в пакет приложения. Эти приложения часто создаются с помощью таких платформ, как Spring Boot, Dropwizard, Micronaut, MicroProfile, Vert.x и т. д.

Эти приложения упаковываются в архивы с расширением *.jar* (JAR-файлы).

### <a name="spring-cloud--microservices"></a>Приложения Spring Cloud (микрослужбы)

Стиль архитектуры микрослужб — это подход к разработке отдельного приложения в виде набора небольших служб, каждая из которых выполняется в собственном процессе и взаимодействует с другими с помощью упрощенных механизмов (как правило, API ресурсов HTTP). Эти службы, созданные на основе бизнес-возможностей, могут независимо развертываться с помощью полностью автоматизированного механизма развертывания. Централизованное управление этими службами сведено к минимуму. Они могут быть написаны на разных языках программирования и использовать различные технологии хранения данных. Такие службы часто создаются на таких платформах, как Spring Cloud.

Эти службы упаковываются в несколько приложений с расширением *.jar* (JAR-файлы).

### <a name="web-applications"></a>Веб-приложения

Веб-приложения выполняются в контейнере [сервлетов](https://en.wikipedia.org/wiki/Java_servlet). Некоторые из них используют непосредственно API-интерфейсы сервлетов, а многие другие — дополнительные платформы, в которые инкапсулированы API сервлетов, например Apache Struts, Spring MVC, JavaServer Faces (JSF) и другие.

Веб-приложения упаковываются в архивы с расширением *.war* (WAR-файлы).

### <a name="java-ee-applications"></a>Приложения Java EE

Приложения Java EE (другое название — приложения J2EE, с недавнего времени — приложения Jakarta EE) могут содержать некоторые или все элементы веб-приложений либо не содержать их вообще. Они также могут содержать и использовать множество дополнительных компонентов, как указано в спецификации [Java EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition).

Приложения Java EE могут упаковываться в архивы с расширением *.ear* (EAR-файлы) или *.war* (WAR-файлы).

Приложения Java EE необходимо развертывать на серверах приложений, совместимых с Java EE (например, WebLogic, WebSphere, WildFly, GlassFish, Payara и др.).

Приложения, в которых используются только функции, предоставляемые спецификацией Java EE (то есть приложения, независимые от сервера приложений), можно переносить с одного совместимого сервера приложений на другой. Если приложение зависит от конкретного сервера приложений, возможно, вам потребуется выбрать целевую службу Azure, в которой можно разместить этот сервер приложений.

### <a name="batch--scheduled-jobs"></a>Пакетные или запланированные задания

Некоторые приложения должны запускаться на короткое время, выполнять определенную рабочую нагрузку и завершаться, а не ожидать запросов или ввода данных пользователем. Такие задания выполняются либо однократно, либо регулярно через определенные интервалы. В локальной среде такие задания часто вызываются из файла crontab на сервере.

Эти приложения упаковываются в архивы с расширением *.jar* (JAR-файлы).

> [!NOTE]
> Если для выполнения запланированных задач в приложении используется планировщик (например, Spring Batch или Quartz), настоятельно рекомендуем учитывать, что такие задачи могут запускаться вне приложения. Если приложение масштабируется до нескольких экземпляров в облаке, одно и то же задание будет выполняться несколько раз. Кроме того, если в вашем механизме планирования используется местный часовой пояс главного компьютера, вы можете столкнуться с нежелательным поведением при масштабировании приложения по регионам.

## <a name="selecting-the-target-azure-service-destination"></a>Выбор целевой службы Azure

В следующих разделах показано, какие целевые службы соответствуют требованиям вашего приложения и какие обязанности с ними связаны.

### <a name="hosting-options-grid"></a>Таблица с вариантами размещения

Приведенная ниже таблица поможет вам определить потенциальные назначения для определенного типа приложений. Как видите, AKS и виртуальные машины поддерживают все типы приложений, но они предъявляют больше требований к вашей команде, как показано в следующем разделе.

|Место назначения&nbsp;→<br><br>Тип&nbsp;приложения&nbsp;↓|Приложение<br>Служба<br>Java SE|Приложение<br>Служба<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Виртуальные<br>машины|
|---|---|---|---|---|---|---|
| Приложения Spring Boot (JAR)                                    |&#x2714;|        |&#x2714;|&#x2714;|&#x2714;|
| Приложения Spring Cloud (микрослужбы)                                      |        |        |&#x2714;|&#x2714;|&#x2714;|
| Веб-приложения                                                  |        |&#x2714;|        |&#x2714;|&#x2714;|
| Приложения Java EE                                              |        |        |        |&#x2714;|&#x2714;|
| Коммерческие серверы приложений<br>(например, WebLogic или WebSphere) |        |        |        |&#x2714;|&#x2714;|
| Долгосрочное хранение в локальной файловой системе                         |&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| Кластеризация на уровне сервера приложений                               |        |        |        |&#x2714;|&#x2714;|
| Пакетные или запланированные задания                                            |        |        |&#x2714;|&#x2714;|&#x2714;|
| интеграция виртуальной сети и гибридные подключения;                              |&#x2714;|&#x2714;|Preview (Предварительный просмотр) |&#x2714;|&#x2714;|
| Доступность по регионам                | [Сведения][10] | [Сведения][10] | [Сведения][11] |[Сведения][12]|[Сведения][13]|

### <a name="ongoing-responsibility-grid"></a>Таблица текущих обязанностей

С помощью следующей таблицы можно выяснить, какие обязанности будут возложены на вашу команду после миграции в зависимости от целевого расположения.

Ваша команда на постоянной основе отвечает за задачи, обозначенные "&#x1F449;". Рекомендуем внедрить надежный автоматизированный процесс для выполнения всех этих обязанностей.

> [!NOTE]
> Этот список обязанностей не является исчерпывающим.

|Место назначения&nbsp;→<br><br>Задача&nbsp;↓                            | Приложение<br>Служба | Azure<br>Spring<br>Cloud | AKS | Виртуальные<br>машины |
|---|---|---|---|---|
| Обновление библиотек<br>(включая устранение уязвимостей)                 | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |
| Обновление сервера приложений<br>(включая устранение уязвимостей)    | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Обновление среды выполнения Java<br>(включая устранение уязвимостей)          | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Активация обновлений Kubernetes<br>(выполняется платформой Azure с помощью ручного триггера) | Недоступно         | ![Azure][1] | &#x1F449;   | Недоступно       |
| Согласование изменений в API Kubernetes, не имеющих обратной совместимости                  | Недоступно         | ![Azure][1] | &#x1F449;   | Недоступно       |
| Обновление базового образа контейнера<br>(включая устранение уязвимостей)      | Недоступно         | ![Azure][1] | &#x1F449;   | Недоступно       |
| Обновление операционной системы<br>(включая устранение уязвимостей)      | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Обнаружение и перезапуск экземпляров после сбоя                                   | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Реализация очистки и перезапуска для обновлений                       | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Управление инфраструктурой                                                   | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Мониторинг и управление оповещениями                                             | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |

Если вы развертываете контейнер сервлетов (например, Spring Boot) в составе своего приложения, вы должны рассматривать его как библиотеку и, следовательно, всегда нести за это ответственность.

## <a name="ensuring-on-premises-connectivity"></a>Обеспечение подключений в локальной среде

Если вашему приложению требуется доступ к какой-либо из локальных служб, вам необходимо подготовить одну из служб подключения Azure. Дополнительные сведения см. в статье [Выбор решения для подключения локальной сети к Azure](/azure/architecture/reference-architectures/hybrid-networking/). Кроме того, необходимо выполнить рефакторинг приложения, чтобы использовать общедоступные API-интерфейсы, предоставляемые локальными ресурсами.

Это нужно сделать до начала миграции.

## <a name="inventory-current-capacity-and-resource-usage"></a>Инвентаризация текущей емкости и потребления ресурсов

Документируйте оборудование текущих рабочих серверов, а также среднее и пиковое количество запросов и потребление ресурсов. Эти сведения понадобятся вам для подготовки ресурсов в целевой службе.

## <a name="migration-guidance"></a>Руководство по миграции

С помощью следующей таблицы можно найти руководство по миграции в зависимости от типа приложения и целевой службы Azure.

**Приложения Java**

В строках ниже можно найти нужный тип приложения Java, а в столбцах — целевую службу Azure, в которой будет размещено ваше приложение.

Если вы хотите перенести приложение JBoss EAP в Tomcat в Службе приложений, сначала преобразуйте приложение Java EE в веб-приложения Java (сервлеты), выполняющиеся на Tomcat, а затем следуйте инструкциями из приведенных ниже руководств.

Если вы хотите перенести веб-приложение, выполняющееся на Tomcat, в Azure Spring Cloud, сначала преобразуйте приложение в микрослужбы Spring Cloud, а затем следуйте инструкциями из приведенных ниже руководств.

|Место назначения&nbsp;→<br><br>Тип&nbsp;приложения&nbsp;↓|Приложение<br>Служба<br>Java SE|Приложение<br>Служба<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Виртуальные машины|
|---|---|---|---|---|---|---|
| Приложения<br>Spring Boot (JAR) | [Руководство][5] | Руководство<br>Планируется | [Руководство][16] | [Руководство][14]      | Руководство<br>Планируется |
| Spring Cloud<br>(микрослужбы)   | Недоступно           | Недоступно                 | [Руководство][15] | Руководство<br>Планируется | Руководство<br>Планируется |
| Веб-приложения<br>в Tomcat     | Недоступно           | [Руководство][2]       | [Руководство][17] | [Руководство][3]       | Руководство<br>Планируется |

**Приложения Java EE**

В строках ниже указаны типы приложений Java EE, выполняющиеся на определенном сервере приложений. В столбцах можно найти целевую службу Azure, в которой будет размещено ваше приложение.

|Место назначения&nbsp;→<br><br>Сервер приложений&nbsp;↓|Приложение<br>Служба<br>Java SE|Приложение<br>Служба<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Виртуальные машины|
|---|---|---|---|---|---|---|
| WildFly /<br>JBoss AS | Недоступно | Недоступно | Недоступно | [Руководство][9] | Руководство<br>Планируется |
| WebLogic              | Недоступно | Недоступно | Недоступно | [Руководство][6] | [Руководство][4]       |
| WebSphere             | Недоступно | Недоступно | Недоступно | [Руководство][7] | Руководство<br>Планируется |
| JBoss EAP             | Недоступно | Недоступно | Недоступно | [Руководство][8] | Руководство<br>Планируется |

<!-- reference links, for use with tables -->
[1]: media/migration-overview/logo_azure.svg
[2]: migrate-tomcat-to-tomcat-app-service.md
[3]: migrate-tomcat-to-containers-on-azure-kubernetes-service.md
[4]: migrate-weblogic-to-virtual-machines.md
[5]: migrate-spring-boot-to-app-service.md
[6]: migrate-weblogic-to-wildfly-on-azure-kubernetes-service.md
[7]: migrate-websphere-to-wildfly-on-azure-kubernetes-service.md
[8]: migrate-jboss-eap-to-wildfly-on-azure-kubernetes-service.md
[9]: migrate-wildfly-to-wildfly-on-azure-kubernetes-service.md
[10]: https://azure.microsoft.com/global-infrastructure/services/?products=app-service-linux
[11]: https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud
[12]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[13]: https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines
[14]: migrate-spring-boot-to-azure-kubernetes-service.md
[15]: migrate-spring-cloud-to-azure-spring-cloud.md
[16]: migrate-spring-boot-to-azure-spring-cloud.md
[17]: migrate-tomcat-to-azure-spring-cloud.md
