---
title: Развертывание веб-приложения Spring Boot в виде файла JAR в Службе приложений Azure на платформе Linux
description: Сведения о развертывании приложения Spring Boot в виде файла JAR в Службе приложений в Linux с помощью подключаемого модуля Maven для веб-приложений Azure.
services: app-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: app-service
ms.topic: article
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: da40d293d0ad42286e6dfb2fd47074a4ae218b6b
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673090"
---
# <a name="deploy-a-spring-boot-jar-file-app-to-azure-app-service-with-maven-and-azure-on-linux"></a>Развертывание приложения Spring Boot в виде файла JAR в Службе приложений Azure с помощью Maven и Azure на платформе Linux

В этом кратком руководстве показано, как использовать [подключаемый модуль Maven для веб-приложений службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme), чтобы развернуть приложение Spring Boot, упакованное в виде файла JAR Java SE, в [Службе приложений Azure в Linux](/azure/app-service/containers/). Развертывание Java SE является предпочтительным по сравнению с [Tomcat и файлами WAR](/azure/app-service/containers/quickstart-java), если вам нужно объединить зависимости, среду выполнения и файлы конфигурации приложения в единый развертываемый артефакт.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения шагов, описанных в этом руководстве, вам понадобиться установить и настроить следующие компоненты:

* [Azure CLI](/cli/azure/) на локальном компьютере или в [Azure Cloud Shell](https://shell.azure.com).
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Apache [Maven](https://maven.apache.org/) версии 3.
* Клиент [Git](https://git-scm.com/downloads).

## <a name="install-and-sign-in-to-azure-cli"></a>Установка и вход в Azure CLI

[Azure CLI](/cli/azure/) — самый простой и наиболее удобный способ развернуть приложение Spring Boot с помощью подключаемого модуля Maven.

Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.
   
   ```shell
   az login
   ```
   
Для завершения процесса входа следуйте инструкциям.

## <a name="clone-the-sample-app"></a>Клонирования примера приложения

Из этого раздела вы узнаете, как клонировать готовое приложение Spring Boot и протестировать его на локальном компьютере.

1. Откройте командную строку или окно терминала и создайте локальный каталог для размещения приложения Spring Boot, после чего перейдите в этот каталог, например:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- или --
   ```shell
   mkdir ~/SpringBoot
   cd ~/SpringBoot
   ```

1. Клонируйте образец проекта [Spring Boot Getting Started] в созданный каталог, например:
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot
   ```

1. Перейдите в каталог готового проекта, например:
   ```shell
   cd gs-spring-boot/complete
   ```

1. Выполните сборку файла JAR с помощью Maven, например:
   ```shell
   mvn clean package
   ```

1. При создании веб-приложения запустите веб-приложение с помощью Maven; например:
   ```shell
   mvn spring-boot:run
   ```

1. Проверьте веб-приложение, перейдя к нему локально с помощью веб-браузера. Например, если имеется Curl, можно использовать следующую команду:
   ```shell
   curl http://localhost:8080
   ```

1. Должно появиться следующее сообщение: **Greetings from Spring Boot!**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Настройка подключаемого модуля Maven для Службы приложений Azure

В этом разделе вы настроите проект Spring Boot `pom.xml`, чтобы развернуть приложение в Службе приложений Azure на платформе Linux с помощью Maven.

1. Откройте файл `pom.xml` в редакторе кода.

2. В разделе `<build>` файла pom.xml добавьте следующую запись `<plugin>` внутри тега `<plugins>`.

   ```xml
   <plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.0</version>
   </plugin>
   ```

3. Затем можно настроить развертывание, выполнить в командной строке приведенную нижу команду Maven и с помощью **number** выбрать параметры в командной строке.
    * **OS**: linux;  
    * **JavaVersion**: Java 8    
    
   ```cmd
   mvn azure-webapp:config
   ```

   Если вы получите запрос **Confirm (Y/N)** , нажмите клавишу **y** для подтверждения. Настройка завершена.

   ```cmd
   ~@Azure:~/gs-spring-boot/complete$ mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO]
   [INFO] -----------------< org.springframework:gs-spring-boot >-----------------
   [INFO] Building gs-spring-boot 0.1.0
   [INFO] --------------------------------[ jar ]---------------------------------
   [INFO]
   [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ gs-spring-boot ---
   [WARNING] The plugin may not work if you change the os of an existing webapp.
   Define value for OS(Default: Linux):
   1. linux [*]
   2. windows
   3. docker
   Enter index to use:
   Define value for javaVersion(Default: Java 8):
   1. Java 11
   2. Java 8 [*]
   Enter index to use:
   Please confirm webapp properties
   AppName : gs-spring-boot-1559091271202
   ResourceGroup : gs-spring-boot-1559091271202-rg
   Region : westeurope
   PricingTier : Premium_P1V2
   OS : Linux
   RuntimeStack : JAVA 8-jre8
   Deploy to slot : false
   Confirm (Y/N)? : Y
   ```

4. Добавьте раздел `<appSettings>` в раздел `<configuration>` в `<azure-webapp-maven-plugin>` для прослушивания порта *80*.

   ```xml
   <plugin>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-webapp-maven-plugin</artifactId>
       <version>1.9.0</version>
       <configuration>
          <schemaVersion>V2</schemaVersion>
          <resourceGroup>gs-spring-boot-1559091271202-rg</resourceGroup>
          <appName>gs-spring-boot-1559091271202</appName>
          <region>westeurope</region>
          <pricingTier>P1V2</pricingTier>
          <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>
            <webContainer>jre8</webContainer>
          </runtime>
          <!-- Begin of App Settings  -->
          <appSettings>
             <property>
                   <name>JAVA_OPTS</name>
                   <value>-Dserver.port=80</value>
             </property>
          </appSettings>
          <!-- End of App Settings  -->
          <deployment>
            <resources>
              <resource>
                <directory>${project.basedir}/target</directory>
                <includes>
                  <include>*.jar</include>
               includes>
              </resource>
            </resources>
          </deployment>
         </configuration>
   </plugin>
   ```

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure

После настройки всех параметров в предыдущих разделах этой статьи можно приступать к развертыванию веб-приложения в Azure. Для этого выполните следующие действия.

1. В командной строке или в окне терминала, которые вы использовали ранее, перестройте JAR-файл, используя Maven, если вы внесли изменения в файл *pom.xml*; например:
   ```shell
   mvn clean package
   ```

1. Разверните веб-приложение в Azure с помощью Maven; например:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven развернет веб-приложение в Azure. Если веб-приложение или план службы приложений отсутствуют, они будут созданы. Отображение веб-приложения по URL-адресу, указанному в выходных данных, может занять несколько минут. В веб-браузере перейдите по URL-адресу.  Должно отобразиться следующее сообщение: Greetings from Spring Boot!

После развертывания веб-приложения вы сможете управлять им с помощью [Портал Azure].

* Веб-приложение будет указано в разделе **Службы приложений**:

   ![Веб-приложение в разделе "Службы приложений" на портале Azure][AP01]

* URL-адрес веб-приложения будет указан в разделе **Обзор** для вашего веб-приложения:

   ![Найдите URL-адрес своего веб-приложения в разделе "Службы приложений" на портале Azure][AP02]

Убедитесь, что развертывание прошло успешно, воспользовавшись описанной выше командой cURL. При этом вместо `localhost` введите URL-адрес веб-приложения, указанный на портале. Должно появиться следующее сообщение: **Greetings from Spring Boot!** 

## <a name="clean-up-resources"></a>Очистка ресурсов
Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

- На портале Azure в меню слева выберите "Группа ресурсов".
- Введите **gs-spring-boot-** в поле **Фильтровать по имени**. Группа ресурсов, созданная при работе с этим руководством, должна иметь этот префикс.
- Выберите группу ресурсов, созданную при работе с этим руководством.
- В меню сверху выберите "Удалить группу ресурсов".

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-esources"></a>Дополнительные ресурсы

Дополнительные сведения о различных технологиях, рассматриваемых в данной статье, см. в следующих статьях.

* [Подключаемый модуль Maven для веб-приложений Azure]

* [Развертывание приложения Spring Boot в Azure с помощью подключаемого модуля Maven для веб-приложений Azure](deploy-containerized-spring-boot-java-app-with-maven-plugin.md)

* [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Справочник по параметрам Maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/developer/java/
[Портал Azure]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot Getting Started]: https://github.com/spring-guides/gs-spring-boot
[Spring Framework]: https://spring.io/
[Подключаемый модуль Maven для веб-приложений Azure]: /java/api/overview/azure/maven/azure-webapp-maven-plugin/readme

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->


[AP01]: media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png