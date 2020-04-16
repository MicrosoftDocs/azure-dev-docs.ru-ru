---
title: Настройка приложения Initializer для начального набора Spring Boot — Azure Monitor
description: Настройка приложения Spring Boot, созданного с помощью Spring Initializr, для использования начального набора Spring Boot в Application Insights.
services: Application-Insights
documentationcenter: java
author: dhaval24
ms.author: dhdoshi
ms.date: 11/29/2019
ms.service: azure-monitor
ms.tgt_pltfrm: application-insights
ms.topic: article
ms.openlocfilehash: 39993902ba01fd92bf44c6ccb85016ef9e73ac97
ms.sourcegitcommit: 8aac5c35fbbd3d954bc4963933f96c93e10623d4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81119536"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-application-insights"></a>Настройка Application Insights в инициализаторе Spring Boot

В этой статье описывается, как создать приложение Spring Boot с помощью **[Spring Initializr]** . Для комплексного мониторинга приложений Java в облаке используется начальный набор Spring Boot для Azure Application Insights.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* API Web Flux и Netty **сейчас не поддерживаются** в начальном приложении Spring Boot для Application Insights.

## <a name="create-a-custom-application-using-spring-initializr"></a>Создание пользовательского приложения с помощью Spring Initializr

Создайте приложение, выполнив следующую процедуру.

1. Перейдите по ссылке [https://start.spring.io/](https://start.spring.io/).

1. Укажите, что вам нужно создать проект **Maven** с использованием **Java**, введите имена **группы** и **артефакта** для своего приложения и в разделе зависимостей выберите веб-зависимости.

   ![Основные параметры Spring Initializr][SI01]

   > [!NOTE]
   >
   > Spring Initializr использует имена **группы** и **артефакта** для создания имени пакета, например, *com.vged.appinsights*.
   >

1. Нажмите кнопку **Generate** (Создать).

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе пользовательское приложение Spring Boot можно будет редактировать.

## <a name="create-an-application-insights-resource-on-azure"></a>Создание ресурса Application Insights в Azure

Создайте ресурс Application Insights, выполнив следующую процедуру.

1. Перейдите в Azure по адресу <https://portal.azure.com/> и щелкните **+Создать ресурс**.

1. Щелкните **ИТ и средства управления** и выберите **Application Insights**.

1. На странице **Новый ресурс Application Insights** укажите следующие сведения:

* Выберите **подписку** и **группу ресурсов**.
* Введите **имя** ресурса Application Insights.
* Выберите **регион**.

   Указав эти параметры, щелкните **Просмотр и создание**.

   ![Azure][AZ03]

* Проверьте спецификации и щелкните **Создать**.

Когда ресурс будет создан, вы увидите его на **панели мониторинга** Azure, а также на странице **Все ресурсы**. В любом из этих расположений щелкните свой ресурс, чтобы открыть страницу обзора ресурса Application Insights.

На странице обзора скопируйте **ключ инструментирования**.

   ![Azure][AZ04]

## <a name="configure-your-downloaded-spring-boot-application-to-use-application-insights"></a>Настройка Application Insights в скачанном приложении Spring Boot

Настройте приложение, используя следующую процедуру.

1. В корневом каталоге приложения найдите файл *POM.xml* и в разделе зависимостей добавьте следующую зависимость:

```XML
 <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
```

1. Найдите файл *application.properties* в каталоге *resources* приложения или создайте файл, если он еще не существует.

1. Откройте файл *application.properties* в текстовом редакторе, добавьте в него указанные ниже строки и замените примеры значений соответствующими свойствами с соответствующими учетными данными.

   ```yaml
   # Specify the instrumentation key of your Application Insights resource.
   azure.application-insights.instrumentation-key=[your ikey from the resource]
   # Specify the name of your springboot application. This can be any logical name you would like to give to your app.
   spring.application.name=[your app name]
   ```

   См. подробнее о точной настройке Application Insights в [файле сведений для начального набора Spring Boot в Application Insights](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

   > [!NOTE]
   > 
   > Используйте разные ключи инструментирования Application Insights (например, разные ресурсы) для разных профилей, таких как PROD, DEV и т. д. См. подробнее о [Свойства профилей Spring Boot]. 

1. Сохраните и закройте файл *application.properties*.

1. Создайте папку с именем *controller* в исходной папке пакета, например:

   `D:\Microsoft\demo\src\main\java\com\example\demo\controller`

   -или-

   `/users/example/home/demo/src/main/java/com/example/demo/controller`

1. В папке *controller* создайте файл с именем *TestController.java*. Откройте файл в текстовом редакторе и добавьте в него следующий код:

   ```java
    package com.example.demo;

    import com.microsoft.applicationinsights.TelemetryClient;
    import java.io.IOException;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    import com.microsoft.applicationinsights.telemetry.Duration;

    @RestController
    @RequestMapping("/sample")
    public class TestController {

        @Autowired
        TelemetryClient telemetryClient;

        @GetMapping("/hello")
        public String hello() {

            //track a custom event  
            telemetryClient.trackEvent("Sending a custom event...");

            //trace a custom trace
            telemetryClient.trackTrace("Sending a custom trace....");

            //track a custom metric
            telemetryClient.trackMetric("custom metric", 1.0);

            //track a custom dependency
            telemetryClient.trackDependency("SQL", "Insert", new Duration(0, 0, 1, 1, 1), true);

            return "hello";
        }
    }
   ```

   Замените `com.example.demo` именем пакета проекта.

1. Сохраните и закройте файл *TestController.java*.

1. Создайте приложение Spring Boot с помощью Maven и запустите его. Пример:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Протестируйте веб-приложение. Для этого перейдите в веб-браузере по адресу http://localhost:8080/sample/hello или используйте следующий синтаксис в **cURL**:

   ```shell
   curl http://localhost:8080/sample/hello
   ```

   Контроллер должен отобразить сообщение "hello!". Application Insights автоматически сохранит этот запрос и отправит его как элемент данных телеметрии вместе со связанным пользовательским событием, пользовательской метрикой, пользовательской зависимостью и пользовательской трассировкой (как указано в логике контроллера). 

   Через несколько секунд вы должны увидеть эти данные на портале Azure. 

   ![Azure][AZ05]

Щелкните плитку **Схема приложений**, чтобы просмотреть высокоуровневые компоненты и их взаимодействие друг с другом. Именно так мы рекомендуем получать общие сведения обо всем приложении. Каждая микрослужба Spring Boot распознается по имени приложения spring. Не забудьте указать его.

   ![Azure][AZ08] 

## <a name="configure-springboot-application-to-send-log4j-logs-to-application-insights"></a>Настройка отправки журналов log4j приложения Springboot в Application Insights

Настройте приложение для отправки журналов, используя следующую процедуру.

1. Измените в проекте файл POM.xml, добавив в раздел зависимостей следующий код: 

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.1</version>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-logging-log4j2</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
```

2. Сохраните и закройте файл *POM.xml*.

3. В папке \src\main\resources создайте файл *log4j2.xml* и настройте его. Пример:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration packages="com.microsoft.applicationinsights.log4j.v2">
  <Properties>
    <Property name="LOG_PATTERN">
      %d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${hostName} --- [%15.15t] %-40.40c{1.} : %m%n%ex
    </Property>
  </Properties>
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <ApplicationInsightsAppender name="aiAppender">
    </ApplicationInsightsAppender>
  </Appenders>
  <Loggers>
    <Root level="trace">
      <AppenderRef ref="Console"  />
      <AppenderRef ref="aiAppender"  />
    </Root>
  </Loggers>
</Configuration>
```

4. Скомпилируйте и запустите приложения Spring Boot еще раз, как показано выше.

Через несколько секунд вы должны увидеть все журналы Spring на портале Azure. Вы можете просматривать подробные сообщения журналов и анализировать их на портале Analytics.

![Azure][AZ07]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Application Insights поддерживает автоматический сбор внешних зависимостей и его корреляцию с входящими запросами. Автоматический сбор сейчас поддерживается для Oracle, MsSQL, MySQL и Redis. Дополнительные сведения о включении автоматического сбора см. в статье [Мониторинг зависимостей, перехваченных исключений и времени выполнения методов в веб-приложениях Java](/azure/application-insights/app-insights-java-agent).

См. подробнее о **[Application Insights]** .

Дополнительные сведения о других настройках начального приложения Spring Boot для Application Insights см. по [этой ссылке](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

Запросы на новые функции и сообщения о потенциальных ошибках оставляйте в нашем репозитории [GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues).

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает процесс создания автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу [https://github.com/spring-guides/](https://github.com/spring-guides/) доступно несколько примеров пакетов этого приложения. Кроме предоставления возможности пользоваться списком основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Azure для разработчиков Java]: /azure/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Свойства профилей Spring Boot]: https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Application Insights]: /azure/application-insights/

<!-- IMG List -->

[AZ01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource.png
[AZ02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_2.png
[AZ03]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_3.png
[AZ04]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Get_IKey.png
[AZ05]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/OverviewBladeResults.png
[AZ06]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/Search_and_traces.png
[AZ07]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/traces_details.png
[AZ08]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/AppMap.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/spring_start.PNG
[SI02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/After_extract.png

[RE01]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationproperties_loc.png
[RE02]: ./media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationinsightsproperties.png
