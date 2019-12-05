---
title: Развертывание приложения Spring Boot с помощью подключаемого модуля Maven Fabric8
description: В этом руководстве содержатся пошаговые инструкции по развертыванию приложения Spring Boot для Apache Maven с помощью подключаемого модуля Maven Fabric8 в Microsoft Azure.
services: container-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: dd6d926b4d4bb21e16fbb922f2daa29cd9d09301
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811880"
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Развертывание приложения Spring Boot с помощью подключаемого модуля Maven Fabric8

**[Fabric8]**  — это решение с открытым кодом на базе **[Kubernetes]** , помогающее разработчикам создавать приложения в контейнерах Linux.

В этом руководстве рассматривается применение подключаемого модуля Fabric8 в Maven для разработки и развертывания приложения на узле Linux в [Служба контейнеров Azure].

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

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Создание веб-приложения Spring Boot on Docker Getting Started

Ниже представлены инструкции по созданию веб-приложения Spring Boot и его локальному тестированию.

1. Откройте командную строку и создайте локальный каталог для размещения приложения, после чего перейдите в этот каталог, например:
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   -- или --
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Клонируйте пример проекта [Spring Boot on Docker Getting Started] в каталог.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта, например:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   -- или --
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Используйте Maven для создания и запуска примера приложения.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Чтобы протестировать веб-приложение, перейдите по адресу http://localhost:8080 или введите такую команду `curl`:
   ```shell
   curl http://localhost:8080
   ```

   Должно появиться следующее сообщение: **Hello Docker World**.

   ![Локальный просмотр примера приложения][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Установка интерфейса командной строки Kubernetes и создание группы ресурсов Azure с помощью Azure CLI

1. Откройте окно командной строки.

1. Введите следующую команду, чтобы войти в учетную запись Azure.
   ```azurecli
   az login
   ```
   Для завершения процесса входа следуйте инструкциям.

   В интерфейсе командной строки Azure отобразится список учетных записей, например:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Если интерфейс командной строки Kubernetes еще не установлен (`kubectl`), можно установить его с помощью Azure CLI, например:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Пользователи Linux могут добавить к этой команде префикс `sudo`, так как она развертывает интерфейс командной строки Kubernetes в `/usr/local/bin`.
   >
   > Если `kubectl` уже установлен и ваша версия `kubectl` устарела, при попытке выполнить действия, приведенные далее в этой статье, может появиться сообщение об ошибке (как в следующем примере).
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > В этом случае необходимо переустановить `kubectl`, чтобы обновить версию.
   >

1. Создайте группу ресурсов Azure, используемых в этой статье, например:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Описание  
      * *wingtiptoys-kubernetes* — уникальное имя для группы ресурсов.  
      * *westeurope* — соответствующее географическое расположение для приложения.  

   В Azure CLI отобразятся результаты созданной группы ресурсов, например:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Создание кластера Kubernetes с помощью Azure CLI

1. Создайте кластер Kubernetes в новой группе ресурсов, например:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Описание  
      * *wingtiptoys-kubernetes* — имя группы ресурсов, описанной в этой статье.  
      * *wingtiptoys-cluster* — уникальное имя для кластера Kubernetes.
      * *wingtiptoys* — уникальное имя DNS-имени для вашего приложения.

   В Azure CLI отобразятся результаты создания кластера, например:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Скачайте учетные данные для нового кластера Kubernetes, например:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Проверьте подключение с помощью следующей команды:
   ```shell 
   kubectl get nodes
   ```

   Появится список узлов и состояний, как в следующем примере:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Создание закрытого реестра контейнеров Azure с помощью Azure CLI

1. Создайте закрытый реестр контейнеров Azure в группе ресурсов для размещения образа Docker, например:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `wingtiptoys-kubernetes` | Определяет имя группы ресурсов, описанной в этой статье. |
   | `wingtiptoysregistry` | Определяет уникальное имя для закрытого реестра. |
   | `westeurope` | Определяет соответствующее географическое расположение для приложения. |

   В Azure CLI отобразятся результаты создания реестра, например:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

2. Получите пароль для реестра контейнеров из Azure CLI.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   В Azure CLI отобразится пароль для реестра, например:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

3. Перейдите в каталог конфигурации для установки Maven (по умолчанию ~/.m2/ или C:\Users\username\.m2) и откройте файл *settings.xml* в текстовом редакторе.

4. Добавьте URL-адрес, имя пользователя и пароль реестра контейнеров Azure для новой коллекции `<server>` в файле *settings.xml*.
   `id` и `username` — это имена реестра. Используйте значение `password` из предыдущей команды (без кавычек).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

5. Перейдите в каталог завершенного проекта для приложения Spring Boot (например, *C:\SpringBoot\gs-spring-boot-docker\complete* или */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*) и откройте файл *pom.xml* в текстовом редакторе.

6. Обновите коллекцию `<properties>` в файле *pom.xml*, добавив значение сервера входа для реестра контейнеров Azure.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

7. Обновите коллекцию `<plugins>` в файле *pom.xml* таким образом, чтобы в `<plugin>` содержались адрес сервера входа и имя реестра контейнеров Azure.

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

8. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните указанную ниже команду Maven для создания контейнера Docker и отправки образа в реестр:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   В Maven отобразятся результаты создания, например:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Настройка приложения Spring Boot для использования подключаемого модуля Maven Fabric8

1. Перейдите в каталог завершенного проекта для приложения Spring Boot (например *C:\SpringBoot\gs-spring-boot-docker\complete* или */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*) и откройте файл *pom.xml* в текстовом редакторе.

1. Обновите коллекцию `<plugins>` в файле *pom.xml*, чтобы добавить подключаемый модуль Maven Fabric8.

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Перейдите в основной исходный каталог для приложения Spring Boot (например *C:\SpringBoot\gs-spring-boot-docker\complete\src\main* или */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*) и создайте папку с именем *fabric8*.

1. Создайте три файла фрагмента YAML в новой папке *fabric8*.

   a. Создайте файл с именем **deployment.yml** со следующим содержимым:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Создайте файл с именем **secrets.yml** со следующим содержимым:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Создайте файл с именем **service.yml** со следующим содержимым:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Запустите следующую команду Maven, чтобы создать файл списка ресурсов Kubernetes.

   ```shell
   mvn fabric8:resource
   ```

   Эта команда объединяет все файлы YAML ресурсов Kubernetes в папке *src/main/fabric8* в файл YAML, содержащий список ресурсов Kubernetes, который можно применить непосредственно к кластеру Kubernetes или экспортировать в чарт Helm.

   В Maven отобразятся результаты создания, например:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Выполните следующую команду Maven, чтобы применить файл списка ресурсов к кластеру Kubernetes:

   ```shell
   mvn fabric8:apply
   ```

   В Maven отобразятся результаты создания, например:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. После развертывания приложения в кластере отправьте запрос к внешнему IP-адресу с помощью приложения `kubectl`, например:
   ```shell
   kubectl get svc -w
   ```

   В `kubectl` отобразятся внутренний и внешний IP-адреса, например:

   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```

   Вы можете использовать внешний IP-адрес, чтобы открыть приложение в веб-браузере.

   ![Просмотр примера приложения извне][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Удаление кластера Kubernetes

Если кластер Kubernetes больше не нужен, можно использовать команду `az group delete`, чтобы удалить группу ресурсов, что приведет к удалению всех связанных ресурсов, например:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Развертывание приложения Spring Boot в Linux в службе контейнеров Azure](deploy-spring-boot-java-app-on-linux.md)
* [Развертывание приложения Spring Boot в кластере Kubernetes в службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Дополнительные сведения о Spring Boot в образце проекта Docker см. в разделе [Spring Boot on Docker Getting Started] (Начало работы с Spring Boot в Docker).

Справку по началу работы с собственными приложениями Spring Boot см. на странице **Spring Initializr**: <https://start.spring.io/>.

Дополнительные сведения о создании простого приложения Spring Boot см. на странице Spring Initializr: <https://start.spring.io/>.

Дополнительные примеры использования пользовательских образов Docker в Azure см. в разделе [Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux].

<!-- URL List -->

[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[Служба контейнеров Azure]: https://azure.microsoft.com/services/container-service/
[Azure для разработчиков Java]: /azure/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/java/ (Работа с Azure DevOps и Java)
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-using-fabric8-maven-plugin/SB02.png
