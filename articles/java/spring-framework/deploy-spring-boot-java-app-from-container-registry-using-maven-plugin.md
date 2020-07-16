---
title: Развертывание приложения Spring Boot в Реестре контейнеров Azure
titleSuffix: Azure App Service
description: В этом руководстве представлен порядок развертывания приложения Spring Boot в реестре контейнеров Azure в службе приложений Azure с помощью подключаемого модуля Maven.
services: container-registry
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 6b23c432767bca6b228e5f68b498f7b4818e9a52
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378588"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Развертывание приложения Spring Boot в Реестре контейнеров Azure в Службе приложений Azure с помощью Maven для веб-приложений Azure

В этой статье описано, как развернуть пример приложения [Spring Boot] в реестре контейнера Azure, а затем использовать подключаемый модуль Maven для веб-приложений Azure для развертывания приложения в службе приложений Azure.

> [!NOTE]
> 
> Подключаемый модуль Maven для веб-приложений Azure для [Apache Maven](https://maven.apache.org/) обеспечивает эффективную интеграцию службы приложений Azure в проекты Maven и упрощает процесс развертывания веб-приложений в службе приложений Azure.
> 
> Подключаемый модуль Maven для веб-приложений Azure в настоящее время доступен в предварительной версии. Сейчас поддерживается только FTP-публикация, но на будущее запланированы дополнительные функции.
> 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется следующее.

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* [Интерфейс командной строки Azure (CLI)].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Средство сборки [Maven] (версия 3) от Apache.
* Клиент [Git].
* Клиент [Docker].

> [!NOTE]
>
> С учетом требований виртуализации для этого руководства изложенные здесь инструкции нельзя выполнять на виртуальной машине. Необходимо использовать физический компьютер с включенными функциями виртуализации.
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Клонирование примера "Приложение Spring Boot в веб-приложении Docker"

В этом разделе представлены сведения о клонировании контейнерного приложения Spring Boot и его тестировании на локальном компьютере.

1. Откройте командную строку или окно терминала и создайте локальный каталог для размещения приложения Spring Boot, после чего перейдите в этот каталог, например:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- или --
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Клонируйте образец проекта [Spring Boot on Docker Getting Started] (Запуск Spring Boot в Docker) в созданный каталог, например:
   ```shell
   https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта, например:
   ```shell
   cd gs-spring-boot-docker/complete
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

1. Должно появиться следующее сообщение: **Hello Docker World**.

   ![Локальный просмотр образца приложения][SB01]

> [!NOTE]
>
> При локальном использовании Docker может появиться сообщение об ошибке подключения к локальному компьютеру через порт 2375. В этом случае попробуйте использовать Docker локально без TLS. Для этого откройте параметры Docker и **предоставьте управляющую программу для TCP://localhost:2375 без TLS**.
>
> ![Предоставление управляющей программы Docker для локального TCP-порта 2375][TL01]

## <a name="create-an-azure-service-principal"></a>Создание субъекта-службы Azure

В этом разделе представлен порядок создания субъекта-службы Azure, которого подключаемый модуль Maven использует при развертывании контейнера в Azure.

1. Откройте командную строку.

2. Войдите в учетную запись Azure с помощью интерфейса командной строки Azure.
   ```azurecli
   az login
   ```
   Для завершения процесса входа следуйте инструкциям.

3. Создайте субъект-службу Azure.
   ```azurecli
   az ad sp create-for-rbac --name <ServicePrincipalName>
   ```
Если параметры аутентификации не указаны, используется аутентификация на основе пароля, который генерируется случайным образом.

4. В ответ Azure предоставит код JSON, аналогичный приведенному ниже.
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > При настройке подключаемого модуля Maven для развертывания контейнера в Azure используйте значения из этого ответа JSON. `aaaaaaaa`, `uuuuuuuu`, `pppppppp` и `tttttttt` являются значениями заполнителя, которые используются в этом примере с целью упростить сопоставление этих значений с соответствующими им элементами во время настройки файла Maven `settings.xml` в следующем разделе.
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Создание реестра контейнеров Azure с помощью Azure CLI

1. Откройте командную строку.

1. Войдите в свою учетную запись Azure.
   ```azurecli
   az login
   ```

1. Создайте группу ресурсов Azure, используемых в этой статье.
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Замените `wingtiptoysresources` в этом примере уникальным именем для группы ресурсов.

1. Создайте частный реестр контейнеров Azure в группе ресурсов для приложения Spring Boot. 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Замените `wingtiptoysregistry` в этом примере уникальным именем для реестра контейнеров.

1. Получите пароль для реестра контейнеров.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   В ответ Azure предоставит пароль, например:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Добавление реестра контейнеров Azure и субъекта-службы Azure в настройки Maven

1. Откройте файл Maven `settings.xml` в текстовом редакторе; этот файл может находиться по пути, аналогичному указанному в следующих примерах.
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. Добавьте параметры доступа к реестру контейнеров Azure из предыдущего раздела этой статьи в коллекцию `<servers>` в файле *settings.xml*, например:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Где:

   |   Элемент    |                                 Описание                                  |
   |--------------|------------------------------------------------------------------------------|
   |    `<id>`    |         Содержит имя закрытого реестра контейнеров Azure.          |
   | `<username>` |         Содержит имя закрытого реестра контейнеров Azure.          |
   | `<password>` | Содержит пароль, полученный в предыдущем разделе этой статьи. |


3. Добавьте параметры субъекта-службы Azure из раздела выше этой статьи в коллекцию `<servers>` в файле *settings.xml*, например:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Где:

   |     Элемент     |                                                                                   Описание                                                                                   |
   |-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `<id>`      |                                Задает уникальное имя, которое Maven использует для поиска параметров безопасности при развертывании веб-приложения в Azure.                                |
   |   `<client>`    |                                                             Содержит значение `appId` из субъекта-службы.                                                             |
   |   `<tenant>`    |                                                            Содержит значение `tenant` из субъекта-службы.                                                             |
   |     `<key>`     |                                                           Содержит значение `password` из субъекта-службы.                                                            |
   | `<environment>` | Определяет целевую облачную среду Azure, которой в этом примере является `AZURE`. (Полный список сред см. в документации по [Подключаемый модуль Maven для веб-приложений Azure].) |


4. Сохраните и закройте файл *settings.xml*.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Сборка образа контейнера Docker и его передача в реестр контейнеров Azure

1. Перейдите в каталог завершенного проекта для приложения Spring Boot (например, "*C:\SpringBoot\gs-spring-boot-docker\complete*" или " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") и откройте файл *pom.xml* в текстовом редакторе.

2. Обновите коллекцию `<properties>` в файле *pom.xml*, добавив значение сервера входа для реестра контейнеров Azure из предыдущего раздела данного учебника, например:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Где:

   |           Элемент           |                                                                       Описание                                                                       |
   |-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `<azure.containerRegistry>` |                                              Задает имя закрытого реестра контейнеров Azure.                                               |
   |   `<docker.image.prefix>`   | Задает URL-адрес закрытого реестра контейнеров Azure, который сформирован путем добавления ".azurecr.io" к имени закрытого реестра контейнеров. |


3. Убедитесь, что в элементе `<plugin>` для подключаемого модуля Docker в файле *pom.xml* содержатся необходимые свойства для адреса сервера входа и имя реестра из предыдущего шага в этом руководстве. Пример:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Где:

   |     Элемент     |                                       Описание                                       |
   |-----------------|-----------------------------------------------------------------------------------------|
   |  `<serverId>`   |  Задает свойство, которое содержит имя закрытого реестра контейнеров Azure.   |
   | `<registryUrl>` | Задает свойство, которое содержит URL-адрес закрытого реестра контейнеров Azure. |


4. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните команду ниже для перестроения приложения и отправки контейнера в реестр контейнеров Azure.

   ```shell
   mvn package docker:build -DpushImage 
   ```

5. НЕОБЯЗАТЕЛЬНО. Перейдите на [портал Azure] и убедитесь, что в реестре контейнеров имеется образ контейнера Docker с именем **gs-spring-boot-docker**.

   ![Проверка контейнера на портале Azure][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Настройка файла pom.xml и последующие построение и развертывание контейнера в Azure

Откройте файл `pom.xml` для приложения Spring Boot в текстовом редакторе, а затем найдите элемент `<plugin>` для `azure-webapp-maven-plugin`. Этот элемент должен выглядеть примерно следующим образом.

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Существует несколько значений, которые можно изменить для подключаемого модуля Maven. Подробное описание каждого из этих элементов см. в документации по [Подключаемый модуль Maven для веб-приложений Azure]. Существует ряд значений, на которые следует обратить внимание в этой статье.

| Элемент | Описание |
|---|---|
| `<version>` | Версия [Подключаемый модуль Maven для веб-приложений Azure]. Обратитесь к списку версий в [центральном репозитории Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22), чтобы убедиться, что вы используете актуальную версию. |
| `<authentication>` | Сведения для проверки подлинности для Azure, в которых в данном примере содержится элемент `<serverId>`, который, в свою очередь, содержит `azure-auth`; Maven использует это значение для поиска значений субъекта-службы Azure в файле Maven *settings.xml*, который вы определили в предыдущем разделе этой статьи. |
| `<resourceGroup>` | Целевая группа ресурсов, которой в этом примере является `wingtiptoysresources`. Если эта группа ресурсов не существует, она будет создана во время развертывания. |
| `<appName>` | Целевое имя веб-приложения. В этом примере целевое имя — `maven-linux-app-${maven.build.timestamp}`, к которому в этом примере добавлен суффикс `${maven.build.timestamp}`, чтобы избежать конфликтов. (Метку времени добавлять необязательно; можно указать любую уникальную строку для имени приложения.) |
| `<region>` | Целевой регион, которым в данном примере является `westus`. (Полный список см. в документации по [Подключаемый модуль Maven для веб-приложений Azure].) |
| `<containerSettings>` | Свойства, которые содержат имя и URL-адрес контейнера. |
| `<appSettings>` | Любые уникальные настройки для Maven, которые следует использовать при развертывании веб-приложения в Azure. В этом примере элемент `<property>` содержит пару "имя/значение" дочерних элементов, которая задает порт для вашего приложения. |

> [!NOTE]
>
> Параметры для изменения номера порта в этом примере необходимы только в случае, если требуется изменить порт по умолчанию.
>

1. В командной строке или в окне терминала, которые вы использовали ранее, перестройте JAR-файл, используя Maven, если вы внесли изменения в файл *pom.xml*; например:
   ```shell
   mvn clean package
   ```

1. Разверните веб-приложение в Azure с помощью Maven; например:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven выполнит развертывание веб-приложения в Azure; если веб-приложение еще не существует, оно будет создано.

> [!NOTE]
>
> Если при запуске развертывания в регионе, который задан в элементе `<region>` в файле *pom.xml*, нет достаточного количества доступных серверов, может появиться сообщение об ошибке, аналогичное приведенному ниже.
>
> ```xml
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> В этом случае можно указать другой регион и повторно выполнить команду Maven для развертывания приложения.
>
>

После развертывания веб-приложения вы сможете управлять им с помощью [Портал Azure].

* Веб-приложение будет указано в разделе **Службы приложений**:

   ![Веб-приложение в разделе "Службы приложений" на портале Azure][AP01]

* URL-адрес веб-приложения будет указан в разделе **Обзор** для вашего веб-приложения:

   ![Определение URL-адреса для веб-приложения][AP02]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о различных технологиях, рассматриваемых в данной статье, см. в следующих статьях.

* [Подключаемый модуль Maven для веб-приложений Azure]

* [Вход в Azure из интерфейса командной строки Azure](/azure/xplat-cli-connect)

* [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Справочник по параметрам Maven](https://maven.apache.org/settings.html)

* [Подключаемый модуль Docker для Maven]

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure для разработчиков Java]: /azure/developer/java/
[Портал Azure]: https://portal.azure.com/
[Подключаемый модуль Maven для веб-приложений Azure]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service/containers/tutorial-custom-docker-image
[Docker]: https://www.docker.com/
[Подключаемый модуль Docker для Maven]: https://github.com/spotify/docker-maven-plugin
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/;
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Maven]: https://maven.apache.org/
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: https://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-sample-spring-boot-app.png
[CR01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-azure-portal-docker-container.png
[AP01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/determine-web-app-url.png
[TL01]: media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/expose-docker-daemon-tcp-port.png
