---
title: Развертывание веб-приложения Spring Boot в Службе приложений Azure для контейнеров
description: В этом руководстве содержатся пошаговые инструкции по развертыванию приложения Spring Boot в качестве веб-приложения Linux в Microsoft Azure.
services: azure app service
documentationcenter: java
ms.date: 12/31/2019
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 943f4c5d859ed31bc7a28b8056855ed4cd2c2a98
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893702"
---
# <a name="deploy-a-spring-boot-application-on-azure-app-service-for-container"></a>Развертывание приложения Spring Boot в Службе приложений Azure для контейнеров

Из этого руководства вы узнаете, как с помощью [Docker] включить приложение [Spring Boot] в контейнер и развернуть образ Docker в узле Linux в [Службе приложений Azure](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).

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

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Создание веб-приложения Spring Boot в Docker

Ниже приведены пошаговые инструкции по созданию простого веб-приложения Spring Boot и его локальному тестированию.

1. Откройте командную строку и создайте локальный каталог для размещения приложения, после чего перейдите в этот каталог, например:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- или --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Клонируйте образец проекта [Spring Boot on Docker Getting Started] (Запуск Spring Boot в Docker) в созданный каталог, например:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта, например:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Выполните сборку файла JAR с помощью Maven, например:
   ```
   mvn package
   ```

1. После создания веб-приложения перейдите в каталог `target`, где находится JAR-файл, и запустите веб-приложение, например:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. Проверьте веб-приложение, перейдя к нему локально с помощью веб-браузера. Например, если у вас установлен CuRL и сервер Tomcat настроен для работы с использованием порта 80:
   ```
   curl http://localhost
   ```

1. Должно появиться следующее сообщение: **Hello Docker World**.

   ![Локальный просмотр образца приложения][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Создание реестра контейнеров Azure для использования в качестве частного реестра Docker

Ниже приведены инструкции по созданию реестра контейнеров Azure с использованием портала Azure.

> [!NOTE]
>
> Если вы хотите использовать Azure CLI, а не портал Azure, выполните процедуру, описанную в разделе [Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0](/azure/container-registry/container-registry-get-started-azure-cli).
>

1. Перейдите на [портал Azure] и выполните вход.

   После входа в свою учетную запись на портале Azure выполните инструкции, приведенные в кратком руководстве по [Создание частного реестра контейнеров Docker с помощью портала Azure], которые здесь полезно представить еще раз.

1. Щелкните значок меню **+ Создать**, затем последовательно выберите **Контейнеры** и **Реестр контейнеров Azure**.
   
   ![Создание нового реестра контейнеров Azure][AR01]

1. Когда откроется страница **Создать реестр контейнеров**, укажите **имя реестра**, **подписку**, **группу ресурсов** и **расположение**. Выберите **Включить** в области **Пользователь-администратор**. Затем нажмите кнопку **Создать**.

   ![Настройка параметров реестра контейнеров Azure][AR03]

1. После создания реестра контейнеров перейдите в реестр контейнеров на портале Azure и щелкните **Ключи доступа**. Запишите имя пользователя и пароль для последующих шагов.

   ![Ключи доступа к реестру контейнеров Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Настройка Maven для использования ключей доступа к реестру контейнеров Azure

1. Перейдите в каталог завершенного проекта для приложения Spring Boot (например "*C:\SpringBoot\gs-spring-boot-docker\complete*" или " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") и откройте файл *pom.xml* в текстовом редакторе.

1. Обновите коллекцию `<properties>` в файле *pom.xml*, добавив последнюю версию [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin), значение сервера входа и параметры доступа для Реестра контейнеров Azure из предыдущего раздела этого руководства. Пример:

   ```xml
   <properties>
      <jib-maven-plugin.version>1.7.0</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <username>wingtiptoysregistry</username>
      <password>{put your Azure Container Registry access key here}</password>
   </properties>
   ```

1. Добавьте [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) в коллекцию `<plugins>` в файле *pom.xml*.  В этом примере используется версия 1.8.0. 

Укажите базовый образ в `<from>/<image>` (здесь это `openjdk:8-jre-alpine`). Укажите имя окончательного образа, который будет создан на основе базового в `<to>/<image>`.  

`{docker.image.prefix}` проверки подлинности — это значение **сервера входа** на странице реестра, показанной выше. `{project.artifactId}` — это имя и номер версии JAR-файла из первой сборки проекта Maven.

Укажите имя пользователя и пароль в области реестра в узле `<to>/<auth>`. Пример:

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>1.8.0</version>
     <configuration>
        <from>
            <image>openjdk:8-jre-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
            <auth>
               <username>${username}</username>
               <password>${password}</password>
            </auth>
        </to>
     </configuration>
   </plugin>
   ```

1. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните команду ниже для перестроения приложения и отправки контейнера в реестр контейнеров Azure:

   ```cmd
   mvn compile jib:build
   ```

> [!NOTE]
>
> При отправке образа в Реестр контейнеров Azure с помощью Jib образ не будет использовать *Dockerfile* (дополнительные сведения см. в [этом](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) документе).
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Создание веб-приложения в Linux в службе приложений Azure с помощью образа контейнера

1. Перейдите на [портал Azure] и выполните вход.

2. Щелкните значок меню **+ Создать ресурс**, затем последовательно выберите **Веб** и **Веб-приложение для контейнеров**.
   
   ![Создание веб-приложения на портале Azure][LX01]

3. Когда отобразится страница **Веб-приложение в Linux**, введите следующие сведения.

   * В раскрывающемся списке выберите свою **подписку**.

   * Выберите существующую **группу ресурсов** или укажите имя, чтобы создать новую группу ресурсов.

   * Введите уникальное имя в поле **Имя приложения**, например *wingtiptoyslinux*.

   * Укажите `Docker Container` для **публикации**.

   * В поле **Операционная система** выберите *Linux*.

   * Выберите **регион**.

   * Примите **план Linux** и выберите существующий **план службы приложений** или щелкните **Создать**, чтобы создать другой план службы приложений.

   * Щелкните **Далее: Docker** (Далее: Docker).

   ![Настройка параметров веб-приложения][LX02]

      На странице **Веб-приложение** выберите **Docker** и введите следующие сведения:

   * Выберите **Один контейнер**.

   * **Реестр**: Выберите свой контейнер, например *wingtiptoysregistry*.

   * **Образ**. Выберите созданный ранее образ, например *gs-spring-boot-docker*.

   * **Тег**. Выберите тег для образа, например *latest*.
   
   * **Команда запуска**: Оставьте это поле пустым, так как образ уже включает команду запуска.
   
   После ввода всех этих данных щелкните **Просмотр и создание**.

   ![Настройка параметров веб-приложения][LX02-A]

   * Щелкните **Review + create** (Просмотреть и создать).
   
Проверьте данные и нажмите кнопку **Создать**.

Когда развертывание будет завершено, выберите **Перейти к ресурсу**.  На странице развертывания отобразится URL-адрес для доступа к приложению.

   ![Получение URL-адреса развертывания][LX02-B]

> [!NOTE]
>
> В Azure интернет-запросы будут автоматически сопоставляться со встроенным сервером Tomcat, который использует порт 80. Если вы настроили встроенный сервер Tomcat так, чтобы он работал с портом 8080 или пользовательским портом, в веб-приложение необходимо добавить переменную среды, которая определяет порт для вашего встроенного сервера Tomcat. Для этого выполните следующие действия.
>
> 1. Перейдите на [портал Azure] и выполните вход.
> 
> 2. Щелкните значок для **веб-приложений** и выберите нужное приложение на странице **Службы приложений**.
>
> 4. В области навигации слева щелкните **Конфигурация**.
>
> 5. В разделе **Параметры приложения** добавьте новый параметр с именем **WEBSITES_PORT** и введите номер пользовательского порта в качестве значения.
>
> 6. Нажмите кнопку **ОК**. Затем нажмите кнопку **Сохранить**.
>
> ![Сохранение номера пользовательского порта на портале Azure][LX03]
>

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Развертывание приложения Spring Boot в кластере Kubernetes в службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Дополнительные сведения о Spring Boot в образце проекта Docker см. в разделе [Spring Boot on Docker Getting Started] (Начало работы с Spring Boot в Docker).

Справку по началу работы с собственными приложениями Spring Boot см. на странице **Spring Initializr**: https://start.spring.io/.

Дополнительные сведения о создании простого приложения Spring Boot см. на странице Spring Initializr: https://start.spring.io/.

Дополнительные примеры использования пользовательских образов Docker в Azure см. в разделе [Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux].

<!-- URL List -->

[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure для разработчиков Java]: /azure/java/
[Портал Azure]: https://portal.azure.com/
[Создание частного реестра контейнеров Docker с помощью портала Azure]: /azure/container-registry/container-registry-get-started-portal
[Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/;
[Working with Azure DevOps and Java]: /azure/devops/java/ (Работа с Azure DevOps и Java)
[Maven]: http://maven.apache.org/
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: ./media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: ./media/deploy-spring-boot-java-app-on-linux/AR03.png
[AR04]: ./media/deploy-spring-boot-java-app-on-linux/AR04.png
[LX01]: ./media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: ./media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: ./media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: ./media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: ./media/deploy-spring-boot-java-app-on-linux/LX03.png
