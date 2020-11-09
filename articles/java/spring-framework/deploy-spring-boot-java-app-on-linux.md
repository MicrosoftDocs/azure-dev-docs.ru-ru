---
title: Развертывание веб-приложения Spring Boot в Службе приложений Azure на платформе Linux
description: В этом руководстве содержатся пошаговые инструкции по развертыванию приложения Spring Boot в качестве веб-приложения Linux в Microsoft Azure.
services: azure app service
documentationcenter: java
ms.date: 10/14/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc, devx-track-java
ms.openlocfilehash: c0baf7ee7b1b672d7eb17bc3a689cc7d58401834
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192496"
---
# <a name="deploy-a-spring-boot-application-to-linux-on-azure-app-service"></a>Развертывание приложения Spring Boot в службе приложений Azure на платформе Linux

Из этого руководства вы узнаете, как с помощью [Docker] включить приложение [Spring Boot] в контейнер и развернуть образ Docker в узле Linux в [Службе приложений Azure](/azure/app-service/containers/app-service-linux-intro).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется следующее.

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* [Интерфейс командной строки Azure (CLI)].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Средство сборки [Apache Maven] версии 3
* Клиент [Git].
* Клиент [Docker].

> [!NOTE]
>
> С учетом требований виртуализации для этого руководства изложенные здесь инструкции нельзя выполнять на виртуальной машине. Необходимо использовать физический компьютер с включенными функциями виртуализации.

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Создание веб-приложения Spring Boot в Docker

Ниже приведены пошаговые инструкции по созданию простого веб-приложения Spring Boot и его локальному тестированию.

1. Откройте командную строку и создайте локальный каталог для размещения приложения, после чего перейдите в этот каталог, например:

   ```bash
   mkdir SpringBoot
   cd SpringBoot
   ```

1. Клонируйте образец проекта [Spring Boot on Docker Getting Started] (Запуск Spring Boot в Docker) в созданный каталог, например:

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта, например:

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Выполните сборку файла JAR с помощью Maven, например:

   ```bash
   mvn package
   ```

1. После создания веб-приложения перейдите в каталог `target`, где находится JAR-файл, и запустите веб-приложение, например:

   ```bash
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. Проверьте веб-приложение, перейдя к нему локально с помощью веб-браузера. Например, если у вас установлен CuRL и сервер Tomcat настроен для работы с использованием порта 80:

   ```bash
   curl http://localhost
   ```

1. Должно появиться следующее сообщение: **Hello Docker World**.

   ![Локальный просмотр образца приложения][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Создание реестра контейнеров Azure для использования в качестве частного реестра Docker

Ниже приведены инструкции по созданию реестра контейнеров Azure с использованием портала Azure.

> [!NOTE]
>
> Если вы хотите использовать Azure CLI, а не портал Azure, выполните процедуру, описанную в разделе [Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0](/azure/container-registry/container-registry-get-started-azure-cli).

1. Перейдите на [портал Azure] и выполните вход.

   После входа в свою учетную запись на портале Azure выполните инструкции, приведенные в кратком руководстве по [Создание частного реестра контейнеров Docker с помощью портала Azure], которые здесь полезно представить еще раз.

1. Щелкните значок меню **Создать** , а затем выберите **Контейнеры** и **Реестр контейнеров Azure**.

   ![Создание нового реестра контейнеров Azure][AR01]

1. Когда откроется страница **Создать реестр контейнеров** , укажите **имя реестра** , **подписку** , **группу ресурсов** и **расположение**. Щелкните **Создать**.

   ![Настройка параметров реестра контейнеров Azure][AR03]

## <a name="configure-maven-to-build-image-to-your-azure-container-registry"></a>Настройка Maven для создания образа в Реестре контейнеров Azure

1. Перейдите в каталог завершенного проекта для приложения Spring Boot (например " *C:\SpringBoot\gs-spring-boot-docker\complete* " или " */users/robert/SpringBoot/gs-spring-boot-docker/complete* ") и откройте файл *pom.xml* в текстовом редакторе.

1. Обновите коллекцию `<properties>` в файле *pom.xml* , добавив последнюю версию [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin), значение сервера входа и параметры доступа для Реестра контейнеров Azure из предыдущего раздела этого руководства. Пример:

   ```xml
   <properties>
      <jib-maven-plugin.version>2.5.2</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Добавьте [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) в коллекцию `<plugins>` в файле *pom.xml*.  В этом примере используется версия 2.2.0.

   Укажите базовый образ в `<from>/<image>` (здесь это `mcr.microsoft.com/java/jre:8-zulu-alpine`). Укажите имя окончательного образа, который будет создан на основе базового в `<to>/<image>`.  

   `{docker.image.prefix}` проверки подлинности — это значение **сервера входа** на странице реестра, показанной выше. `{project.artifactId}` — это имя и номер версии JAR-файла из первой сборки проекта Maven.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jre:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните команду ниже для перестроения приложения и отправки контейнера в реестр контейнеров Azure:

   ```bash
   az acr login -n wingtiptoysregistry && mvn compile jib:build
   ```

> [!NOTE]
> 1. Команда `az acr login ...` выполнит попытку входа в Реестр контейнеров Azure. В противном случае потребуется указать `<username>` и `<password>` для jib-maven-plugin. Дополнительные сведения см. в разделе о [методах проверки подлинности](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#authentication-methods) в репозитории Jib.
> 2. При отправке образа в Реестр контейнеров Azure с помощью Jib образ не будет использовать *Dockerfile* (дополнительные сведения см. в [этом](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) документе).
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Создание веб-приложения в Linux в службе приложений Azure с помощью образа контейнера

1. Перейдите на [портал Azure] и выполните вход.

2. Щелкните значок меню **Создать ресурс** , а затем выберите **Вычисления** и **Веб-приложение для контейнеров**.
   
   ![Создание веб-приложения на портале Azure][LX01]

3. Когда отобразится страница **Веб-приложение в Linux** , введите следующие сведения.

   * В раскрывающемся списке выберите свою **подписку**.

   * Выберите существующую **группу ресурсов** или укажите имя, чтобы создать новую группу ресурсов.

   * Введите уникальное имя в поле **Имя приложения** , например *wingtiptoyslinux*.

   * Укажите `Docker Container` для **публикации**.

   * В поле **Операционная система** выберите *Linux*.

   * Выберите **регион**.

   * Примите **план Linux** и выберите существующий **план службы приложений** или щелкните **Создать** , чтобы создать новый.

   * Щелкните **Далее: Docker** (Далее: Docker).

   ![Щелкните "Далее: Docker", чтобы продолжить.][LX02]

      На странице **Веб-приложение** выберите **Docker** и введите следующие сведения:

   * Выберите **Один контейнер**.

   * **Реестр** : Выберите свой контейнер, например *wingtiptoysregistry*.

   * **Образ**. Выберите созданный ранее образ, например *gs-spring-boot-docker*.

   * **Тег**. Выберите тег для образа, например *latest*.

   * **Команда запуска** : Оставьте это поле пустым, так как образ уже включает команду запуска.

   Введя эти данные, щелкните **Просмотр и создание**.

   ![Для завершения щелкните "Просмотр и создание".][LX02-A]

   * Щелкните **Review + create** (Просмотреть и создать).

Просмотрите сведения и щелкните **Создать**.

Когда развертывание будет завершено, выберите **Перейти ресурсу**.  На странице развертывания отобразится URL-адрес для доступа к приложению.

   ![Получение URL-адреса развертывания][LX02-B]

> [!NOTE]
>
> В Azure интернет-запросы будут автоматически сопоставляться со встроенным сервером Tomcat, который использует порт 80. Если вы настроили встроенный сервер Tomcat так, чтобы он работал с портом 8080 или пользовательским портом, в веб-приложение необходимо добавить переменную среды, которая определяет порт для вашего встроенного сервера Tomcat. Для этого выполните следующие действия.
>
> 1. Перейдите на [портал Azure] и выполните вход.
>
> 2. Щелкните значок **Веб-приложения** и выберите нужное приложение на странице **Службы приложений**.
>
> 3. В области навигации слева щелкните **Конфигурация**.
>
> 4. В разделе **Параметры приложения** добавьте новый параметр с именем **WEBSITES_PORT** и введите номер пользовательского порта в качестве значения.
>
> 5. Щелкните **ОК**. Затем нажмите кнопку **Save** (Сохранить).
>
> ![Сохранение номера пользовательского порта на портале Azure][LX03]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Дополнительные сведения о Spring Boot в образце проекта Docker см. в разделе [Spring Boot on Docker Getting Started] (Начало работы с Spring Boot в Docker).

Справку по началу работы с собственными приложениями Spring Boot см. на странице **Spring Initializr** : https://start.spring.io/.

Дополнительные сведения о создании простого приложения Spring Boot см. на странице Spring Initializr: https://start.spring.io/.

Дополнительные примеры использования пользовательских образов Docker в Azure см. в разделе [Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux].

<!-- URL List -->

[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure для разработчиков Java]: ../index.yml
[Портал Azure]: https://portal.azure.com/
[Создание частного реестра контейнеров Docker с помощью портала Azure]: /azure/container-registry/container-registry-get-started-portal
[Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux]: /azure/app-service/tutorial-custom-container
[Docker]: https://www.docker.com/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/;
[Working with Azure DevOps and Java]: /azure/devops/java/ (Работа с Azure DevOps и Java)
[Maven]: http://maven.apache.org/
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: ../fundamentals/java-jdk-long-term-support.md
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: media/deploy-spring-boot-java-app-on-linux/AR03.png
[LX01]: media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: media/deploy-spring-boot-java-app-on-linux/LX03.png
