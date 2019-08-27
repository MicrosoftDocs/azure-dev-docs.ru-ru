---
title: Развертывание приложения Spring Boot в виде JAR-файла в Azure с помощью Maven
description: Узнайте, как развернуть приложение Spring Boot в облаке с помощью подключаемого модуля Maven для веб-приложений Azure на платформе Linux.
services: app-service
documentationcenter: java
author: bmitchell287
manager: douge
editor: brborges
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: app-service
ms.topic: article
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 05d9fb7817eafea56e095667c260075c5e02c6da
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889813"
---
# <a name="deploy-a-spring-boot-web-app-to-azure-app-service-on-linux"></a>Развертывание веб-приложения Spring Boot в Службе приложений Azure на платформе Linux

В этой статье показано, как использовать [подключаемый модуль Maven для веб-приложений службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme), чтобы развернуть приложение Spring Boot, упакованное в виде файла JAR Java SE, в [Службе приложений Azure на платформе Linux](/azure/app-service/containers/). Развертывание Java SE является предпочтительным по сравнению с [Tomcat и файлами WAR](/azure/app-service/containers/quickstart-java) в тех случаях, когда нужно объединить зависимости, среду выполнения и файлы конфигурации приложения в единый артефакт, пригодный для развертывания.


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
   md ~/SpringBoot
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
    <version>1.6.0</version>
   </plugin>
   ```

3. Затем можно настроить развертывание, выполнить в командной строке команду Maven `mvn azure-webapp:config` и с помощью **number** выбрать параметры в командной строке:
    * **OS**: linux;  
    * **javaVersion**: jre8;
    * **runtimeStack**: jre8.

Если вы получите запрос **Confirm (Y/N)** , нажмите клавишу **y** для подтверждения. Настройка завершена.

```cmd
~@Azure:~/gs-spring-boot/complete$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] -----------------< org.springframework:gs-spring-boot >-----------------
[INFO] Building gs-spring-boot 0.1.0
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.6.0:config (default-cli) @ gs-spring-boot ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use: 2
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
       <version>1.6.0</version>
       <configuration>
          <schemaVersion>V2</schemaVersion>
          <resourceGroup>gs-spring-boot-1559091271202-rg</resourceGroup>
          <appName>gs-spring-boot-1559091271202</appName>
          <region>westeurope</region>
          <pricingTier>P1V2</pricingTier>

          <!-- Begin of App Settings  -->
          <appSettings>
             <property>
                   <name>JAVA_OPTS</name>
                   <value>-Dserver.port=80</value>
             </property>
          </appSettings>
          <!-- End of App Settings  -->
          ...
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

Maven развернет веб-приложение в Azure. Если веб-приложение или план службы приложений отсутствуют, они будут созданы.

После развертывания веб-приложения вы сможете управлять им с помощью [портал Azure].

* Веб-приложение будет указано в разделе **Службы приложений**:

   ![Веб-приложение в разделе "Службы приложений" на портале Azure][AP01]

* URL-адрес веб-приложения будет указан в разделе **Обзор** для вашего веб-приложения:

   ![Определение URL-адреса для веб-приложения][AP02]

Убедитесь, что развертывание прошло успешно, воспользовавшись описанной выше командой cURL. При этом вместо `localhost` введите URL-адрес веб-приложения, указанный на портале. Должно появиться следующее сообщение: **Greetings from Spring Boot!** 

## <a name="clean-up-resources"></a>Очистка ресурсов
Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

- На портале Azure в меню слева выберите "Группа ресурсов".
- Введите **gs-spring-boot-** в поле **Фильтровать по имени**. Группа ресурсов, созданная при работе с этим руководством, должна иметь этот префикс.
- Выберите группу ресурсов, созданную при работе с этим руководством.
- В меню сверху выберите "Удалить группу ресурсов".

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о различных технологиях, рассматриваемых в данной статье, см. в следующих статьях.

* [Подключаемый модуль Maven для веб-приложений Azure]

* [Развертывание приложения Spring Boot в Azure с помощью подключаемого модуля Maven для веб-приложений Azure](deploy-containerized-spring-boot-java-app-with-maven-plugin.md)

* [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Справочник по параметрам Maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/java/
[портал Azure]: https://portal.azure.com/
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

[AP01]: ./media/deploy-spring-boot-java-app-with-maven-plugin/AP01.png
[AP02]: ./media/deploy-spring-boot-java-app-with-maven-plugin/AP02.png
