---
title: Развертывание веб-приложения Payara Micro в Службе приложений Azure с помощью Maven
description: Сведения о развертывании приложения Payara Micro в Службе приложений в Linux с помощью подключаемого модуля Maven для веб-приложений Azure.
services: app-service
documentationcenter: java
ms.date: 06/10/2020
ms.service: app-service
ms.topic: article
ms.custom: ''
ms.openlocfilehash: ad425d79767af78448e99b6d0e29dea839c19972
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754090"
---
# <a name="deploy-a-payara-micro-web-app-to-azure-app-service-with-maven"></a>Развертывание веб-приложения Payara Micro в Службе приложений Azure с помощью Maven

В этом кратком руководстве показано, как использовать [подключаемый модуль Maven для веб-приложений Службы приложений Azure](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md), чтобы развернуть приложение Payara Micro в [Службе приложений Azure в Linux](/azure/app-service/containers/). Развертывание Java SE является предпочтительным по сравнению с [Tomcat и файлами WAR](/azure/app-service/containers/quickstart-java), если вам нужно объединить зависимости, среду выполнения и файлы конфигурации приложения в единый развертываемый артефакт.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* [Azure CLI](/cli/azure/) на локальном компьютере или в [Azure Cloud Shell](https://shell.azure.com).
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Apache [Maven](https://maven.apache.org/) версии 3.

## <a name="install-and-sign-in-to-azure-cli"></a>Установка и вход в Azure CLI

[Azure CLI](/cli/azure/) — самый простой и наиболее удобный способ развернуть приложение Payara Micro с помощью подключаемого модуля Maven.

Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.

```azurecli
az login
```

Для завершения процесса входа следуйте инструкциям.

## <a name="create-sample-app-from-microprofile-starter"></a>Создание примера приложения в MicroProfile Starter

В этом разделе вы создадите приложение Payara Micro и протестируете его локально.

1. Откройте веб-браузер и перейдите на сайт [MicroProfile Starter](https://start.microprofile.io/).

   ![MicroProfile Starter для Payara Micro](./media/payara-micro/microprofile-starter-payara-micro.png)

2. Введите или выберите поле, как показано ниже.  

   | Поле  |  Значение  |
   | ---- | ---- |
   |  groupId  |  com.microsoft.azure.samples.payaramicro  |
   |  artifactId  |  payaramicro-hello-azure  |
   |  MicroProfile Version (Версия MicroProfile)  |  MP 3.2  |
   |  Java SE Version (Версия Java SE)  |  Java 11  |
   |  MicroProfile Runtime (Среда выполнения MicroProfile)  |  Payara Micro  |
   |  Examples for Specifications (Примеры для требований)  |  Метрики, OpenAPI  |

3. Нажмите кнопку **СКАЧАТЬ**, чтобы скачать проект.

4. Распакуйте файл архива, например:

   ```bash
   unzip payaraMicro-hello-azure.zip
   ```

### <a name="run-the-application-in-local-environment"></a>Запуск приложения в локальной среде

1. Перейдите в каталог готового проекта, например:

   ```bash
   cd payaramicro-hello-azure/
   ```

2. Создайте проект с помощью Maven; например:

   ```bash
   mvn clean package
   ```

3. Запустите проект, например:

   ```bash
   java -jar target/payaramicro-hello-azure-microbundle.jar
   ```

4. Проверьте веб-приложение, перейдя к нему локально с помощью веб-браузера. Например, если имеется Curl, можно использовать следующую команду:

   ```bash
   curl http://localhost:8080/data/hello
   ```

5. Должно появиться следующее сообщение: **Привет, мир!**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Настройка подключаемого модуля Maven для Службы приложений Azure

В рамках этого раздела вы настроите файл *pom.xml* проекта Payara Micro, чтобы развернуть приложение в Службе приложений Azure на платформе Linux с помощью Maven.

1. Откройте файл *pom.xml* в текстовом редакторе.

2. В разделе `<build>` файла *pom.xml* добавьте следующую запись `<plugin>` внутри тега `<plugins>`.

   ```xml
   <build>
     <finalName>payaramicro-hello-azure</finalName>
     <plugins>
       <plugin>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>azure-webapp-maven-plugin</artifactId>
           <version>1.10.0</version>
       </plugin>
     </plugins>
   </build>
   ```

3. Затем можно настроить развертывание, выполнить в командной строке приведенную нижу команду Maven и с помощью **number** выбрать параметры в командной строке.

   ```cmd
   mvn azure-webapp:config
   ```

   Параметр настройки:  

   |  Поле ввода  |  Введите или выберите значение  |
   | ---- | ---- |
   |  Определите значение для ОС (Значение по умолчанию: Linux):  | 1. Linux  |
   |  Определите значение для javaVersion (Значение по умолчанию: Java 8):   | 1. Java 11  |
   |  Определите значение для runtimeStack (Значение по умолчанию: TOMCAT 8.5): | TOMCAT 8.5 |
   |  Подтверждение (Y/N) | да |

   [!NOTE]
   Несмотря на то, что мы не используем TOMCAT, выбрать `TOMCAT 8.5`? Позже во время более детальной настройки мы поменяем значение `TOMCAT 8.5` на `Java11`.**

   Настройку можно выполнить с помощью следующей команды:

   ```cmd
   mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO] 
   [INFO] --< com.microsoft.azure.samples.payaramicro:payaramicro-hello-azure >---
   [INFO] Building payaramicro-hello-azure 1.0-SNAPSHOT
   [INFO] --------------------------------[ war ]---------------------------------
   [INFO] 
   [INFO] --- azure-webapp-maven-plugin:1.10.0:config (default-cli) @ payaramicro-hello-azure ---
   Define value for OS(Default: Linux): 
   1. linux [*]
   2. windows
   3. docker
   Enter index to use: 
   Define value for javaVersion(Default: Java 8): 
   1. Java 11
   2. Java 8 [*]
   Enter index to use: 1
   Define value for runtimeStack(Default: TOMCAT 8.5): 
   1. TOMCAT 9.0
   2. TOMCAT 8.5 [*]
   Enter index to use: 
   Please confirm webapp properties
   AppName : payaramicro-hello-azure-1601009217863
   ResourceGroup : payaramicro-hello-azure-1601009217863-rg
   Region : westeurope
   PricingTier : PremiumV2_P1v2
   OS : Linux
   RuntimeStack : TOMCAT 8.5-java11
   Deploy to slot : false
   Confirm (Y/N)? : y
   [INFO] Saving configuration to pom.
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  22.302 s
   [INFO] Finished at: 2020-09-25T13:47:11+09:00
   [INFO] ------------------------------------------------------------------------
   ```

4. Измените среду выполнения с `TOMCAT 8.5` на `java11` и файл развертывания с `*.war` на `*.jar`. Затем добавьте раздел `<appSettings>` в `<configuration>` пунктов `PORT`, `WEBSITES_PORT` и `WEBSITES_CONTAINER_START_TIME_LIMIT`.  
 Наконец, можно увидеть следующую XML-запись для `azure-webapp-maven-plugin`.

    ```xml
    <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>1.9.1</version>
      <configuration>
        <schemaVersion>V2</schemaVersion>
        <resourceGroup>microprofile</resourceGroup>
        <appName>payaramicro-hello-azure-1591860934798</appName>
        <pricingTier>P1v2</pricingTier>
        <region>japaneast</region>
        <runtime>
          <os>linux</os>
          <javaVersion>java11</javaVersion>
          <webContainer>java11</webContainer>
        </runtime>
        <appSettings>
          <property>
            <name>PORT</name>
            <value>8080</value>
          </property>
            <property>
            <name>WEBSITES_PORT</name>
            <value>8080</value>
          </property>
          <property>
            <name>WEBSITES_CONTAINER_START_TIME_LIMIT</name>
            <value>600</value>
          </property>
        </appSettings>
        <deployment>
          <resources>
            <resource>
              <directory>${project.basedir}/target</directory>
              <includes>
                <include>*.jar</include>
              </includes>
            </resource>
          </resources>
        </deployment>
      </configuration>
    </plugin>
   ```

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure

После настройки всех параметров в предыдущих разделах этой статьи можно приступать к развертыванию веб-приложения в Azure. Для этого выполните следующие действия.

1. В командной строке или в окне терминала, которые вы использовали ранее, перестройте JAR-файл, используя Maven, если вы внесли изменения в файл *pom.xml*; например:

   ```bash
   mvn clean package
   ```

2. Разверните веб-приложение в Azure с помощью Maven; например:

   ```bash
   mvn azure-webapp:deploy
   ```

   Если развертывание прошло успешно, на консоли вы увидите следующее сообщение.

   ```bash
   mvn azure-webapp:deploy

   [INFO] Successfully deployed the artifact to https://payaramicro-hello-azure-1601009217863.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:58 min
   [INFO] Finished at: 2020-09-25T13:55:13+09:00
   [INFO] ------------------------------------------------------------------------
   ```

   Maven развернет веб-приложение в Azure. Если веб-приложение или план службы приложений отсутствуют, они будут созданы. Отображение веб-приложения по URL-адресу, указанному в выходных данных, может занять несколько минут. В веб-браузере перейдите по URL-адресу.  Вы должны увидеть приведенный ниже экран.

   ![Основная страница Payara Micro](./media/payara-micro/payara-micro-front-page.png)

   После развертывания веб-приложения вы сможете управлять им с помощью [портала Azure].

   * Веб-приложение будет указано в группе ресурсов **microprofile**:

   ![Веб-приложение в разделе "Службы приложений" на портале Azure](./media/payara-micro/payara-micro-azure-portal-rg.png)

   * И вы можете получить доступ к своему веб-приложению, нажав кнопку `Browse` в разделе **Обзор** веб-приложения.  
Убедитесь, что развертывание было успешным и запущено. Вы должны увидеть следующий экран:

   ![Найдите URL-адрес своего веб-приложения в разделе "Службы приложений" на портале Azure](./media/payara-micro/payara-micro-azure-portal-manage.png)

## <a name="confirm-the-log-stream-from-running-app-service"></a>Подтверждение потока журналов в запущенной Службе приложений
 
Вы можете просмотреть журналы (или выполнить вывод последнего фрагмента) из запущенной Службы приложений. Все вызовы `console.log` в коде сайта отображаются в окне терминала.
 
   ```azurecli
   az webapp log tail -g microprofile -n payaramicro-hello-azure-1601009217863
   ```
 
   ![Подтверждение потока журнала](./media/payara-micro/azure-cli-app-service-log-stream.png)
 
## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

* На портале Azure в меню слева выберите "Группа ресурсов".
* Введите **microprofile** в поле **Фильтровать по имени**. Группа ресурсов, созданная при работе с этим учебником, должна иметь этот префикс.
* Выберите группу ресурсов, созданную при работе с этим руководством.
* В меню сверху выберите "Удалить группу ресурсов".

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о MicroProfile и Azure см. в центре документации об использовании MicroProfile в Azure.

> [!div class="nextstepaction"]
> [Документация по Eclipse MicroProfile в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о различных технологиях, рассматриваемых в данной статье, см. в следующих статьях.

* [Подключаемый модуль Maven для веб-приложений Azure]

* [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Справочник по параметрам Maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: ../index.yml
[Портал Azure]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Подключаемый модуль Maven для веб-приложений Azure]: https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md

[Java Development Kit (JDK)]: ../fundamentals/java-jdk-long-term-support.md
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png