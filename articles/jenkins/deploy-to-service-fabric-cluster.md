---
title: Руководство. Развертывание в кластере Service Fabric
description: Узнайте, как настроить непрерывную сборку и интеграцию приложения Service Fabric для Linux с помощью Jenkins.
keywords: Jenkins, Azure, DevOps, CI/CD, Linux, Service Fabric, кластер
ms.topic: tutorial
ms.date: 07/31/2018
ms.custom: devx-track-jenkins
ms.openlocfilehash: 6ce3714220a5c1cdff3a40cb8590a36356167616
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240866"
---
# <a name="tutorial-deploy-to-a-service-fabric-cluster"></a>Руководство по Развертывание в кластере Service Fabric

В этом учебнике описывается несколько способов настройки среды Jenkins, а также различные способы развертывания приложения в кластере Service Fabric после его сборки. Выполните приведенные общие инструкции, чтобы успешно настроить Jenkins, получить изменения из GitHub, выполнить сборку приложения и развернуть его в кластере.

1. Убедитесь, что у вас установлены [необходимые компоненты](#prerequisites).
1. Затем выполните инструкции в одном из приведенных ниже разделов, чтобы настроить Jenkins.
   * [Настройка Jenkins в кластере Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Настройка Jenkins за пределами кластера Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)
   * [Установка подключаемого модуля Service Fabric в существующей среде Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. После настройки Jenkins выполните инструкции в разделе [Создание и настройка задания Jenkins](#create-and-configure-a-jenkins-job), чтобы настроить GitHub для активации Jenkins при внесении изменений в приложение и чтобы настроить конвейер заданий Jenkins на этапе сборки для получения изменений из GitHub и выполнения сборки приложения. 
1. Наконец, настройте действие задания Jenkins после сборки для развертывания приложения в кластере Service Fabric. Существуют два способа настройки Jenkins для развертывания приложения в кластере.    
   * Для сред разработки и тестирования используйте [настройку развертывания с помощью конечной точки управления кластером](#configure-deployment-using-cluster-management-endpoint). Это простейший способ развертывания, который можно настроить.
   * Для рабочих сред используйте [настройку развертывания с помощью учетных данных Azure](#configure-deployment-using-azure-credentials). Корпорация Майкрософт рекомендует этот способ для рабочих сред, так как учетные данные Azure позволяют ограничить доступ задания Jenkins к ресурсам Azure. 

## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что компонент Git установлен в локальной среде. Соответствующую вашей операционной системе версию Git можно установить, скачав ее со [страницы загрузок Git](https://git-scm.com/downloads). Если вы не знакомы с Git, ознакомьтесь со сведениями, приведенными в [этой документации](https://git-scm.com/docs).
- В этой статье для выполнения сборки и развертывания приложения используется *пример для начала работы с Service Fabric* с сайта GitHub ([https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started)). Можно разветвить этот репозиторий, чтобы продолжить изучение этого руководства, или, внеся некоторые изменения в инструкции, использовать собственный проект GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Установка подключаемого модуля Service Fabric в существующей среде Jenkins

Если вы добавляете подключаемый модуль Service Fabric в имеющуюся среду Jenkins, необходимо сделать следующее.

- [Интерфейс командной строки Service Fabric (sfctl)](/azure/service-fabric/service-fabric-cli). Установите интерфейс командной строки на уровне системы, а не на уровне пользователя, чтобы среда Jenkins могла выполнять команды интерфейса командной строки. 
- Для развертывания приложений Java установите [Gradle и Open JDK 8.0](/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development). 
- Для развертывания приложений .NET Core 2.0 установите [пакет SDK для .NET Core 2.0](/azure/service-fabric/service-fabric-get-started-linux#set-up-net-core-20-development). 

После установки необходимых компонентов для своей среды можно будет найти подключаемый модуль Azure Service Fabric на сайте marketplace Jenkins и установить его.

После установки подключаемого модуля перейдите к [созданию и настройке задания Jenkins](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Настройка Jenkins в кластере Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как его настроить внутри кластера при использовании учетной записи хранения Azure для сохранения состояния экземпляра контейнера.

1. Убедитесь, что у вас есть кластер Service Fabric под управлением Linux с установленным компонентом Docker. Компонент Docker уже установлен на кластерах Service Fabric, запущенных в Azure. Если кластер запущен локально (в среде разработки OneBox), проверьте, установлен ли на компьютере компонент Docker, выполнив команду `docker info`. Если он не установлен, установите его, выполнив следующие команды.

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Убедитесь, что в качестве пользовательской конечной точки в кластере задан порт 8081. При использовании локального кластера убедитесь, что на хост-компьютере открыт порт 8081 и ему назначен общедоступный IP-адрес.
   >

1. Клонируйте приложение, выполнив следующие команды.
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Сохраните состояние контейнера Jenkins в общей папке:
   1. Создайте учетную запись хранения Azure, например с именем `sfjenkinsstorage1`, в **том же регионе**, что и кластер.
   1. Затем создайте **общую папку** для этой учетной записи хранения и присвойте ей имя, например `sfjenkins`.
   1. Щелкните **Подключить** для общей папки. Запишите значения, которые отображаются в разделе **Подключение из Linux**. Они должны выглядеть приблизительно так:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Для подключения файловых ресурсов cifs требуется установить пакет cifs-utils на узлах кластера.         
   >

1. Замените значения заполнителей в сценарии `setupentrypoint.sh` сведениями из учетной записи хранения Azure, приведенными на шаге 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Замените `[REMOTE_FILE_SHARE_LOCATION]` значением `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` из выходных данных подключения, полученных выше на шаге 2.
   * Замените `[FILE_SHARE_CONNECT_OPTIONS_STRING]` значением `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`, полученным ранее на шаге 2.

1. **Только для безопасного кластера** 
   
   Для настройки развертывания приложений в безопасном кластере из службы Jenkins сертификат кластера должен быть доступен в контейнере Jenkins. В файле *ApplicationManifest* под тегом **ContainerHostPolicies** добавьте эту ссылку на сертификат и измените значение отпечатка, указав отпечаток сертификата кластера.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Кроме того, добавьте приведенные ниже строки под (корневым) тегом **ApplicationManifest** в файле *ApplicationManifest.xml* и измените значение отпечатка, указав отпечаток сертификата кластера.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Подключитесь к кластеру и установите приложение-контейнер.

   **Безопасный кластер**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Предыдущая команда принимает сертификат в формате PEM. Если сертификат имеет формат PFX, можно использовать следующую команду, чтобы преобразовать его. Если PFX-файл не защищен паролем, укажите параметр **passin** следующим образом: `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Небезопасный кластер**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Контейнер Jenkins установится в кластер, и его можно будет отслеживать с помощью Service Fabric Explorer.

   > [!NOTE]
   > Скачивание образа Jenkins в кластер может занять несколько минут.
   >

1. Откройте в браузере `http://PublicIPorFQDN:8081`. Это предоставит путь к начальному паролю администратора, необходимому для входа в систему. 
1. Откройте Service Fabric Explorer, чтобы узнать, на каком узле выполняется контейнер Jenkins. Войдите на узел по протоколу SSH.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Получите идентификатор экземпляра контейнера с помощью `docker ps -a`.
1. Войдите в контейнер по протоколу SSH и вставьте путь, предоставленный на портале Jenkins. Например, если на портале был указан путь `PATH_TO_INITIAL_ADMIN_PASSWORD`, выполните следующие команды.

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. На главной странице Jenkins щелкните параметре "Select plugins to install" (Выбор подключаемых модулей для установки), установите флажок **None** (Нет) и нажмите кнопку "Install" (Установить).
1. Создайте учетную запись пользователя или продолжите от имени администратора.

После настройки Jenkins перейдите к [созданию и настройке задания Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Настройка Jenkins за пределами кластера Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как настроить Jenkins за пределами кластера.

1. Убедитесь, что на компьютере установлен компонент Docker, запустив `docker info` в терминале. Выходные данные указывают, запущена ли служба Docker.

1. Если компонент Docker не установлен, выполните следующую команду:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Получите образ контейнера Jenkins для Service Fabric: `docker pull rapatchi/jenkins:latest`. Этот образ предварительно установлен в подключаемом модуле Jenkins Service Fabric.
1. Запустите образ контейнера: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Получите идентификатор экземпляра образа контейнера. Вы можете вывести список всех контейнеров Docker с помощью команды `docker ps –a`.
1. Войдите на портал Jenkins, сделав следующее.

   1. Войдите в оболочку Jenkins со своего узла. Используйте первые четыре разряда идентификатора контейнера. Например, если идентификатор контейнера — `2d24a73b5964`, то используйте `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. В оболочке Jenkins получите пароль администратора для экземпляра контейнера.

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Чтобы войти в панель мониторинга Jenkins, откройте следующий URL-адрес в браузере: `http://<HOST-IP>:8080`. Введите пароль из предыдущего шага, чтобы получить доступ к Jenkins.
   1. (Необязательно.) После первого входа можно создать собственную учетную запись и использовать ее в будущем или же продолжать использовать учетную запись администратора. Если вы создадите пользователя, вам потребуется использовать его для последующих действий.
1. Настройте GitHub для работы с Jenkins, выполнив шаги, описанные в статье [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Создание ключа SSH и его добавление в агент SSH).
   * Используйте инструкции, предоставленные в GitHub, чтобы создать ключ SSH и добавить его в учетную запись GitHub, в которой находится репозиторий.
   * В оболочке Jenkins или Docker (а не на узле) выполните команды, перечисленные в приведенной выше статье.
   * Чтобы войти в оболочку Jenkins с вашего узла, выполните следующую команду:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Убедитесь, что кластер или компьютер, где размещен образ контейнера Jenkins, имеет общедоступный IP-адрес, чтобы экземпляр Jenkins получал уведомления от GitHub.

После настройки Jenkins перейдите к следующему разделу, [Создание и настройка задания Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Создание и настройка задания Jenkins

В этом разделе описывается, как настроить задание Jenkins, чтобы можно было реагировать на изменения в репозитории GitHub, получать их и выполнять сборку с этими изменениями. В конце этого раздела вы будете направлены к заключительным действиям по настройке задания для развертывания приложения в среде разработки и тестирования или рабочей среде. 

1. На панели мониторинга Jenkins щелкните **New Item** (Создать элемент).
1. Введите имя элемента, например **MyJob**. Выберите **проект в свободной форме** и нажмите кнопку **ОК**.
1. Откроется страница настройки задания. (Чтобы получить конфигурацию из панели мониторинга Jenkins, выберите задание и нажмите кнопку **Configure** (Настройка).)

1. На вкладке **General** (Общие) установите флажок напротив **проекта GitHub** и укажите URL-адрес этого проекта. Это URL-адрес, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать процесс непрерывной интеграции и доставки Jenkins (например, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. На вкладке **Source Code Management** (Управление исходным кодом) выберите **Git**. Укажите URL-адрес репозитория, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать процесс непрерывной интеграции и доставки Jenkins (например, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Также можно указать ветвь для выполнения сборки (например, `/master`).
1. Настройте репозиторий *GitHub* для обмена данными с Jenkins.

   1. На странице репозитория GitHub выберите **Settings** (Параметры)  > **Integrations and Services** (Интеграция и службы).

   1. Выберите **Add Service** (Добавить службу), введите **Jenkins** и выберите **Jenkins-Github plugin** (Модуль Jenkins-Github).

   1. Введите URL-адрес webhook для Jenkins (по умолчанию это должен быть `http://<PublicIPorFQDN>:8081/github-webhook/`). **Добавьте или обновите службу**.

   1. В ваш экземпляр Jenkins будет отправлено событие проверки. Рядом с webhook в GitHub должен появиться зеленый флажок. Это означает, что проект успешно создан.

1. На вкладке **Build Triggers** (Триггеры сборки) в Jenkins выберите требуемый вариант сборки. Например, необходимо активировать выполнение сборки при каждой отправке изменений в репозиторий, поэтому выберите **GitHub hook trigger for GITScm polling** (Триггер обработчика GitHub для опроса GITScm). (Ранее был указан параметр **Build when a change is pushed to GitHub** (Выполнять сборку при отправке изменений в GitHub).)
1. На вкладке **Build** (Сборка) выполните одно из следующих действий, в зависимости от того, какое приложение вы создаете: Java или .NET Core.

   * **Для приложений Java**. Из раскрывающегося списка **Add build step** (Добавление шага сборки) выберите **Invoke Gradle Script** (Вызов сценария Gradle). Щелкните **Дополнительно**. В расширенном меню укажите путь к **корневому сценарию сборки** своего приложения. Этот скрипт выбирает build.gradle по указанному пути и действует соответствующим образом. Для [приложения ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter) это `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Действие при сборке Jenkins для Service Fabric](./media/deploy-to-service-fabric-cluster/build-step.png)

   * **Для приложений .NET Core**. Из раскрывающегося списка **Add build step** (Добавление шага сборки) выберите **Execute Shell** (Выполнение оболочки). В отобразившемся окне команд сначала необходимо перейти в каталог, в котором находится файл `build.sh`. После этого можно будет запустить скрипт `build.sh` для сборки приложения.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  
      ./build.sh
      ```

     Ниже приведен снимок экрана с примером команд, которые используются для сборки примера [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) с заданием Jenkins `CounterServiceApplication`.

      ![Действие при сборке Jenkins для Service Fabric](./media/deploy-to-service-fabric-cluster/build-step-dotnet.png)

1. Чтобы настроить Jenkins для развертывания приложения в кластере Service Fabric во время действий после сборки, необходимо расположение сертификата этого кластера в контейнере Jenkins. Выберите одно из приведенных ниже действий в зависимости от того, запущен ли контейнер Jenkins в кластере или за его пределами, и запишите расположение сертификата кластера.

   * **Для контейнера Jenkins, выполняющегося в кластере**. Путь к сертификату можно найти, выводя значение переменной среды *Certificates_JenkinsOnSF_Code_MyCert_PEM* из контейнера.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Для контейнера Jenkins, выполняющегося вне кластера**. Выполните следующие действия, чтобы скопировать сертификат кластера в контейнер.
     1. Сертификат должен быть в формате PEM. При отсутствии PEM-файла его можно создать из PFX-файла сертификата. Если PFX-файл не защищен паролем, выполните следующую команду на узле.

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Если PFX-файл защищен паролем, укажите его в параметре `-passin`. Пример:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Чтобы получить идентификатор контейнера Jenkins, выполните команду `docker ps` на узле.
     1. Скопируйте PEM-файл в контейнер с помощью следующей команды Docker.
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Почти все готово! Не закрывайте задание Jenkins. Осталось только настроить действия после сборки для развертывания приложения в кластере Service Fabric.

* Чтобы развернуть его в среде разработки или тестирования, выполните действия, описанные в разделе [Настройка развертывания с использованием конечной точки управления кластером](#configure-deployment-using-cluster-management-endpoint).
* Чтобы развернуть его в рабочей среде, выполните действия, описанные в разделе [Настройка развертывания с использованием учетных данных Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Настройка развертывания с использованием конечной точки управления кластером

Для развертывания приложения в средах разработки и тестирования можно использовать конечную точку управления кластером. Чтобы настроить действие после сборки с использованием конечной точки управления кластером для развертывания приложения, требуется минимальная настройка. Если необходимо развернуть приложение в рабочей среде, перейдите к разделу [Настройка развертывания с использованием учетных данных Azure](#configure-deployment-using-azure-credentials), чтобы настроить субъект-службу Azure Active Directory для развертывания.    

1. В задании Jenkins выберите вкладку **Post-build Actions** (Действия после сборки). 
1. В раскрывающемся списке **Post-Build Actions** (Действия после сборки) выберите **Deploy Service Fabric Project** (Развернуть проект Service Fabric). 
1. В разделе **Service Fabric Cluster Configuration** (Конфигурация кластера Service Fabric) выберите переключатель **Fill the Service Fabric Management Endpoint** (Заполнить данные конечной точки управления Service Fabric).
1. Для параметра **Management Host** (Узел управления) введите конечную точку подключения для своего кластера, например `{your-cluster}.eastus.cloudapp.azure.com`.
1. Для параметров **Client Key** (Ключ клиента) и **Client Cert** (Сертификат клиента) укажите расположение PEM-файла в контейнере Jenkins, например `/var/jenkins_home/clustercert.pem`. (Вы скопировали расположение сертификата на заключительном шаге [создания и настройки задания Jenkins](#create-and-configure-a-jenkins-job).)
1. В разделе **Application Configuration** (Конфигурация приложения) настройте параметры **Application Name** (Имя приложения), **Application Type** (Тип приложения) и **Path to Application Manifest** (Путь к манифесту приложения) (относительный).

   ![Настройка конечной точки управления для действия после сборки Jenkins в Service Fabric](./media/deploy-to-service-fabric-cluster/post-build-endpoint.png)

1. Щелкните **Verify configuration**(Проверить конфигурацию). При успешной проверки щелкните **Save** (Сохранить). Теперь конвейер заданий Jenkins полностью настроен. Перейдите к [дальнейшим действиям](#next-steps) для тестирования развертывания.

## <a name="configure-deployment-using-azure-credentials"></a>Настройка развертывания с использованием учетных данных Azure

В рабочих средах настоятельно рекомендуется настроить учетные данные Azure для развертывания приложения. В этом разделе показано, как настроить субъект-службу Azure Active Directory для развертывания приложения с помощью действия после сборки. Можно назначить субъекты-службы ролям в вашем каталоге, чтобы ограничить разрешения для задания Jenkins. 

Для развертывания приложения в средах разработки и тестирования можно настроить учетные данные Azure либо конечную точку управления кластером. Дополнительные сведения о настройке конечной точки управления кластером см. в разделе [Настройка развертывания с использованием конечной точки управления кластером](#configure-deployment-using-cluster-management-endpoint).   

1. Чтобы создать субъект-службу Azure Active Directory и назначить ему разрешения в подписке Azure, выполните действия, описанные в разделе [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Обратите внимание на следующее.

   * При выполнении действий, описанных в этой статье, не забудьте скопировать и сохранить следующие значения: *идентификатор приложения*, *ключ приложения*, *идентификатор каталога (идентификатор арендатора)* и *идентификатор подписки*. Они нужны для настройки учетных данных Azure в Jenkins.
   * Если у вас нет [необходимых разрешений](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) для каталога, то вам потребуется обратиться к администратору, чтобы он предоставил их или создал субъект-службу для вас. Или вам потребуется настроить конечную точку управления для кластера в **действиях после сборки** для задания в Jenkins.
   * В разделе [Создание приложения Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) можно ввести любой URL-адрес с правильным форматом в поле **URL-адрес входа**.
   * В разделе [Назначение роли приложению](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) можно назначить приложению роль *Читатель* для группы ресурсов кластера.

1. В задании Jenkins выберите вкладку **Post-build Actions** (Действия после сборки).
1. В раскрывающемся списке **Post-Build Actions** (Действия после сборки) выберите **Deploy Service Fabric Project** (Развернуть проект Service Fabric). 
1. В разделе **Service Fabric Cluster Configuration** (Конфигурация кластера Service Fabric) щелкните **Select the Service Fabric Cluster** (Выбрать кластер Service Fabric). Нажмите кнопку **Add** (Добавить) рядом с разделом **Azure Credentials** (Учетные данные Azure). Щелкните **Jenkins**, чтобы выбрать поставщик учетных данных Jenkins.
1. В диалоговом окне поставщика учетных данных Jenkins из раскрывающегося списка **Kind** (Тип) выберите **Microsoft Azure Service Principal** (Субъект-служба Microsoft Azure).
1. Используйте значения, которые вы сохранили при настройке субъекта-службы на шаге 1, чтобы заполнить следующие поля.

   * **Идентификатор клиента**: *идентификатор приложения*.
   * **Секрет клиента**: *ключ приложения*.
   * **Идентификатор арендатора**: *идентификатор каталога*.
   * **Идентификатор подписки**: *Идентификатор подписки*
1. Введите описательный **идентификатор**, используемый для выбора учетных данных в Jenkins, и добавьте краткое **описание**. Затем щелкните **Verify Service Principal** (Проверить субъект-службу). Если проверка прошла, нажмите кнопку **Add** (Добавить).

   ![Ввод учетных данных Azure для Jenkins на платформе Service Fabric](./media/deploy-to-service-fabric-cluster/enter-azure-credentials.png)
1. На вкладке **Service Fabric Cluster Configuration** (Конфигурация кластера Service Fabric) убедитесь, что для параметра **Azure Credentials** (Учетные данные Azure) выбраны ваши новые учетные данные. 
1. Из раскрывающегося списка **Resource Group** (Группа ресурсов) выберите группу ресурсов кластера, в которой необходимо развернуть приложение.
1. Из раскрывающегося списка **Service Fabric** выберите кластер, в котором необходимо развернуть приложение.
1. В полях **Client Key** (Ключ клиента) и **Client Cert** (Сертификат клиента) укажите расположение PEM-файла в контейнере Jenkins. Например, `/var/jenkins_home/clustercert.pem`. 
1. В разделе **Application Configuration** (Конфигурация приложения) настройте параметры **Application Name** (Имя приложения), **Application Type** (Тип приложения) и **Path to Application Manifest** (Путь к манифесту приложения) (относительный).
    ![Настройка учетных данных Azure для действия после сборки Jenkins в Service Fabric](./media/deploy-to-service-fabric-cluster/post-build-credentials.png)
1. Щелкните **Verify configuration**(Проверить конфигурацию). При успешной проверки щелкните **Save** (Сохранить). Теперь конвейер заданий Jenkins полностью настроен. Перейдите к [дальнейшим действиям](#next-steps) для тестирования развертывания.

## <a name="troubleshooting-the-jenkins-plugin"></a>Устранение неполадок подключаемого модуля Jenkins

Если вы столкнулись с ошибками, которые касаются подключаемых модулей Jenkins, сообщите о проблеме конкретного компонента в [JENKS JIRA](https://issues.jenkins-ci.org/).

## <a name="ideas-to-try"></a>Апробация идей

Теперь GitHub и Jenkins настроены, Попробуйте внести несколько изменений в проект `reliable-services-actor-sample/Actors/ActorCounter` в своей вилке репозитория, https://github.com/Azure-Samples/service-fabric-java-getting-started. Передайте эти изменения в удаленную ветвь `master` (или ветвь, настроенную для работы). Это запустит задание Jenkins `MyJob`, которое вы настроили. Оно получит изменения из GitHub, выполнит их сборку и развернет приложение в кластере, выбранном в действиях после сборки.  

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/Jenkins/)
