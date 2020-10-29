---
title: Развертывание приложения Spring Boot Application в Службе Azure Kubernetes
titleSuffix: Azure Kubernetes Service
description: В этом руководстве содержатся пошаговые инструкции по развертыванию приложения Spring Boot в кластере Kubernetes в Microsoft Azure.
services: container-service
documentationcenter: java
ms.date: 10/06/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: d902219a7dd5f59abc54343ff3aca60f4b7f46e4
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689193"
---
# <a name="deploy-spring-boot-application-to-the-azure-kubernetes-service"></a>Развертывание приложения Spring Boot Application в Службе Azure Kubernetes

**[Kubernetes]** и **[Docker]** — это решения с открытым кодом, которые помогают разработчикам автоматизировать развертывание и масштабирование выполняемых в контейнерах приложений, а также управление ими.

В этом руководстве представлены пошаговые инструкции по объединению этих двух популярных технологий с открытым кодом для разработки и развертывания приложения Spring Boot в Microsoft Azure. В частности, *[Spring Boot]* используется для разработки приложений, *[Kubernetes]* — для развертывания контейнеров, а [Служба Azure Kubernetes (AKS)] — для размещения приложений.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* [Интерфейс командной строки Azure (CLI)].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* Средство сборки [Maven] (версия 3) от Apache.
* Клиент [Git].
* Клиент [Docker].
* [Вспомогательное приложение для учетных данных ACR Docker](https://github.com/Azure/acr-docker-credential-helper).

> [!NOTE]
> С учетом требований виртуализации для этого руководства изложенные здесь инструкции нельзя выполнять на виртуальной машине. Необходимо использовать физический компьютер с включенными функциями виртуализации.

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Создание веб-приложения Spring Boot в Docker

Ниже представлены инструкции по созданию веб-приложения Spring Boot и его локальному тестированию.

1. Откройте командную строку и создайте локальный каталог для размещения приложения, после чего перейдите в этот каталог, например:

   ```bash
   mkdir C:\SpringBoot
   cd C:\SpringBoot
   ```

   -- или --

   ```bash
   mkdir /users/$USER/SpringBoot
   cd /users/$USER/SpringBoot
   ```

1. Клонируйте пример проекта [Spring Boot on Docker Getting Started] в каталог.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта.

   ```bash
   cd gs-spring-boot-docker
   cd complete
   ```

1. Используйте Maven для создания и запуска примера приложения.

   ```bash
   mvn package spring-boot:run
   ```

1. Чтобы протестировать веб-приложение, перейдите по адресу `http://localhost:8080` или введите такую команду `curl`:

   ```bash
   curl http://localhost:8080
   ```

1. Должно появиться следующее сообщение: **Hello Docker World** .

   ![Локальный просмотр образца приложения][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Создание реестра контейнеров Azure с помощью Azure CLI

1. Откройте командную строку.

1. Войдите в свою учетную запись Azure.

   ```azurecli
   az login
   ```

1. Выберите подписку Azure:

   ```azurecli
   az account set -s <YourSubscriptionID>
   ```

1. Создайте группу ресурсов Azure, используемых в этом руководстве.

   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Создайте частный реестр контейнеров Azure в группе ресурсов. Позднее в руководстве пример принудительно отправляется в этот реестр как образ Docker. Замените `wingtiptoysregistry` уникальным именем для реестра.

   ```azurecli
   az acr create --resource-group wingtiptoys-kubernetes --location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Принудительная отправка приложения в реестр контейнеров с использованием Jib

1. Войдите в Реестр контейнеров с помощью Azure CLI.

   ```azurecli
   # set the default name for Azure Container Registry, otherwise you will need to specify the name in "az acr login"
   az configure --defaults acr=wingtiptoysregistry
   az acr login
   ```

1. Откройте файл *pom.xml* в текстовом редакторе, например [VS Code](https://code.visualstudio.com/docs).

   ```bash
   code pom.xml
   ```

1. Обновите коллекцию `<properties>` в файле *pom.xml* , добавив имя для своего реестра в Реестре контейнеров Azure и последнюю версию [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <!-- Note: If your ACR name contains upper case characters, be sure to convert them to lower case characters. -->
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>2.5.2</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Обновите коллекцию `<plugins>` в файле *pom.xml* , чтобы элемент `<plugin>` содержал запись `jib-maven-plugin`, как показано в следующем примере. Обратите внимание, что мы используем базовый образ из Реестра контейнеров Майкрософт (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, который содержит официально поддерживаемый пакет JDK для Azure. Другие базовые образы MCR с официально поддерживаемыми пакетами JDK см. в статьях [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) и [Java SE JDK и Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните указанную ниже команду для создания образа и его отправки в реестр:

   ```cmd
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
> Из-за особенностей, связанных с безопасностью Azure CLI и Реестром контейнеров Azure, учетные данные, созданные командой `az acr login`, действуют в течение всего 1 часа. При появлении ошибки *401 — недостаточно прав* вы можете повторно выполнить команду `az acr login -n <your registry name>` для аутентификации.

## <a name="create-a-kubernetes-cluster-on-aks-using-the-azure-cli"></a>Создание в Службе контейнеров Azure кластера Kubernetes с помощью Azure CLI

1. Создайте кластер Kubernetes в Службе Azure Kubernetes. Следующая команда отвечает за создание кластера *kubernetes* в группе ресурсов *wingtiptoys-kubernetes* с именем кластера *wingtiptoys-akscluster* , присоединенным реестром службы "Реестр контейнеров Azure" (ACR) `wingtiptoysregistry` и префиксом DNS *wingtiptoys-kubernetes* :

   ```azurecli
   az aks create --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster \
    --attach-acr wingtiptoysregistry \
    --dns-name-prefix=wingtiptoys-kubernetes --generate-ssh-keys
   ```

   Выполнение этой команды может занять некоторое время.

1. Установите `kubectl` с использованием Azure CLI. Пользователи Linux могут добавить к этой команде префикс `sudo`, так как она развертывает интерфейс командной строки Kubernetes в `/usr/local/bin`.

   ```azurecli
   az aks install-cli
   ```

1. Скачайте сведения о конфигурации кластера, чтобы управлять им из веб-интерфейса Kubernetes и `kubectl`. 

   ```azurecli
   az aks get-credentials --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Развертывание образа в кластере Kubernetes

В этом учебнике с помощью `kubectl` развертывается приложение, а затем предоставляется возможность изучить развертывание с помощью веб-интерфейса Kubernetes.

### <a name="deploy-with-kubectl"></a>Развертывание с помощью kubectl

1. Откройте командную строку.

1. Запустите контейнер в кластере Kubernetes с помощью команды `kubectl run`. Присвойте приложению имя службы в Kubernetes и полное имя образа. Пример:

   ```bash
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```

   В этой команде:

   * Имя контейнера `gs-spring-boot-docker` указано сразу после команды `run`.

   * Параметр `--image` указывает объединенное имя сервера входа и образа как `wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`.

1. Предоставьте кластер Kubernetes извне с помощью команды `kubectl expose`. Укажите имя службы, общедоступный TCP-порт, используемый для доступа к приложению, и внутренний целевой порт, прослушиваемый приложением. Пример:

   ```bash
   kubectl expose pod gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```

   В этой команде:

   * Имя контейнера `gs-spring-boot-docker` указано сразу после команды `expose pod`.

   * Параметр `--type` указывает, что кластер использует подсистему балансировки нагрузки.

   * Параметр `--port` указывает общедоступный TCP-порт 80. Через этот порт вы осуществляете доступ к приложению.

   * Параметр `--target-port` указывает общедоступный TCP-порт 8080. Через этот порт подсистема балансировки нагрузки переадресовывает запросы к приложению.

1. После развертывания приложения в кластере подайте запрос на внешний IP-адрес и откройте его в своем веб-браузере:

   ```bash
   kubectl get services -o=jsonpath='{.items[*].status.loadBalancer.ingress[0].ip}'
   ```

   ![Просмотр примера приложения в Azure][SB02]

### <a name="deploy-with-the-kubernetes-web-interface"></a>Развертывание с помощью веб-интерфейса Kubernetes

1. Откройте командную строку.

1. Откройте веб-сайт конфигурации кластера Kubernetes в браузере по умолчанию:

   ```azurecli
   az aks browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```

> [!IMPORTANT]
> Если кластер AKS использует RBAC, необходимо создать *ClusterRoleBinding* (связи кластерных ролей) прежде чем правильно подключиться к панели мониторинга. По умолчанию панель мониторинга Kubernetes развертывается с минимальными правами доступа на чтение и отображает сообщения об ошибках доступа RBAC. Сейчас панель мониторинга Kubernetes не поддерживает пользовательские учетные данные для определения уровня доступа. Вместо этого она использует роли, предоставленные учетной записи службы. Администратор кластера может предоставить дополнительный доступ к учетной записи службы *kubernetes-dashboard* , однако это может послужить вектором для эскалации привилегий. Вы также можете интегрировать аутентификацию Azure Active Directory, чтобы предоставить более детальный уровень доступа.
>
> Чтобы создать привязку, используйте команду [kubectl create clusterrolebinding]. В приведенном ниже примере показано, как создать пример привязки. Но этот пример привязки не предусматривает использование дополнительных компонентов проверки подлинности и может привести к небезопасному использованию. Панель мониторинга Kubernetes доступна для любого пользователя, имеющего доступ к URL-адресу. Не предоставляйте публичный доступ к панели мониторинга Kubernetes.
>
> ```bash
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
>
> Дополнительные сведения об использовании разных методов проверки подлинности см. в вики-статье о панели мониторинга Kubernetes: [dashboard-authentication].

1. Когда в браузере откроется веб-сайт для настройки Kubernetes, щелкните ссылку, чтобы **развернуть контейнерное приложение** :

   ![Веб-сайт настройки Kubernetes с сообщением о том, что нет данных для отображения][KB01]

1. Когда отобразится страница **Resource Creation** (Создание ресурса), укажите следующие параметры:

   а. Выберите **Create an App** (Создать приложение).

   b. Укажите имя приложения Spring Boot в поле **Имя приложения** (например, *gs-spring-boot-docker* ).

   c. Укажите сервер входа и образ контейнера, заданные ранее, в поле **Образ контейнера** (например, *wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest* ).

   d. Для параметра **Служба** выберите значение **Внешняя** .

   д) Укажите внешний и внутренний порты в текстовых полях **Порт** и **Целевой порт** .

   ![Веб-сайт конфигурации Kubernetes: страница "Создание приложения"][KB02]

1. Нажмите кнопку **Deploy** (Развернуть), чтобы развернуть контейнер.

   ![Развертывание Kubernetes][KB05]

1. После развертывания приложение Spring Boot отобразится в списке **Службы** .

   ![Веб-сайт Kubernetes: список служб][KB06]

1. Щелкнув ссылку для **внешних конечных точек** , можно просмотреть сведения о выполнении приложения Spring Boot в Azure.

   ![Веб-сайт Kubernetes: список служб с выделенными внешними конечными точками][KB07]

   ![Просмотр примера приложения в Azure][SB02]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Spring Boot в Azure см. в следующей статье:

* [Развертывание приложения Spring Boot в Службе приложений Azure в Linux](deploy-spring-boot-java-app-on-linux.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Дополнительные сведения о развертывании приложения Java в кластере Kubernetes с помощью Visual Studio Code см. в [Руководства по Java для Visual Studio Code].

Дополнительные сведения о примере проекта Spring Boot в Docker см. в разделе [Spring Boot on Docker Getting Started].

По следующим ссылкам представлены дополнительные сведения о создании приложений Spring Boot:

* Дополнительные сведения о создании простого приложения Spring Boot см. на странице Spring Initializr по адресу https://start.spring.io/.

По следующим ссылкам представлены дополнительные сведения об использовании Kubernetes с Azure:

* [Начало работы с кластером Kubernetes в Службе Azure Kubernetes](/azure/aks/intro-kubernetes)

Дополнительные сведения об использовании интерфейса командной строки Kubernetes доступны в руководстве пользователя **kubectl** по адресу <https://kubernetes.io/docs/user-guide/kubectl/>.

На сайте Kubernetes содержится несколько статей, посвященных использованию образов в частных реестрах:

* [Configure Service Accounts for Pods] (Настройка учетных записей службы для модулей Pod)
* [Пространства имен]
* [Pull an Image from a Private Registry] (Извлечение образа из частного реестра)

Дополнительные примеры использования пользовательских образов Docker в Azure см. в разделе [Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux].

Дополнительные сведения об итеративном выполнении и отладке контейнеров непосредственно в Службе Azure Kubernetes (AKS) с помощью Azure Dev Spaces см. в разделе [Начало работы в Azure Dev Spaces с использованием Java].

<!-- URL List -->
[kubectl create clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[Служба Azure Kubernetes (AKS)]: https://azure.microsoft.com/services/kubernetes-service/
[Azure для разработчиков Java]: ../index.yml
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux]: /azure/app-service/tutorial-custom-container
[Docker]: https://www.docker.com/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/;
[Working with Azure DevOps and Java]: /azure/devops/java/ (Работа с Azure DevOps и Java)
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Configure Service Accounts for Pods]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ (Настройка учетных записей службы для модулей Pod)
[Пространства имен]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Pull an Image from a Private Registry]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/ (Извлечение образа из частного реестра)

[Java Development Kit (JDK)]: ../fundamentals/java-jdk-long-term-support.md
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- Newly added -->
[Authenticate with Azure Container Registry from Azure Kubernetes Service]: /azure/container-registry/container-registry-auth-aks/
[Руководства по Java для Visual Studio Code]: https://code.visualstudio.com/docs/java/java-kubernetes/
[Начало работы в Azure Dev Spaces с использованием Java]: /azure/dev-spaces/get-started-java
<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-kubernetes/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-kubernetes/SB02.png

[AR01]: media/deploy-spring-boot-java-app-on-kubernetes/AR01.png
[AR02]: media/deploy-spring-boot-java-app-on-kubernetes/AR02.png
[AR03]: media/deploy-spring-boot-java-app-on-kubernetes/AR03.png
[AR04]: media/deploy-spring-boot-java-app-on-kubernetes/AR04.png

[KB01]: media/deploy-spring-boot-java-app-on-kubernetes/KB01.png
[KB02]: media/deploy-spring-boot-java-app-on-kubernetes/KB02.png
[KB03]: media/deploy-spring-boot-java-app-on-kubernetes/KB03.png
[KB04]: media/deploy-spring-boot-java-app-on-kubernetes/KB04.png
[KB05]: media/deploy-spring-boot-java-app-on-kubernetes/KB05.png
[KB06]: media/deploy-spring-boot-java-app-on-kubernetes/KB06.png
[KB07]: media/deploy-spring-boot-java-app-on-kubernetes/KB07.png
