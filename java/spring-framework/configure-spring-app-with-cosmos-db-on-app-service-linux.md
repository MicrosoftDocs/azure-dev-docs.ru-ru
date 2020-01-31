---
title: Использование Spring и Cosmos DB со Службой приложений в Linux
description: В этой статье описан процесс сборки, настройки, развертывания, устранения неполадок и масштабирования веб-приложений Java в Службе приложений Azure в Linux.
documentationcenter: java
ms.reviewer: joshuapa
ms.date: 4/24/2019
ms.service: cosmos-db
ms.topic: article
ms.openlocfilehash: 53bc7d9ebd03fb122d58dd52145876e698dc751f
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76872169"
---
# <a name="how-to-use-spring-and-cosmos-db-with-app-service-on-linux"></a>Использование Spring и Cosmos DB со Службой приложений в Linux

В этой статье описан процесс сборки, настройки, развертывания, устранения неполадок и масштабирования веб-приложений Java в Службе приложений Azure в Linux.

Здесь продемонстрировано использование следующих компонентов:

- [начальное приложение Spring Boot с API SQL Azure Cosmos DB](configure-spring-boot-starter-java-app-with-cosmos-db.md);
- [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-introduction)
- [Служба приложений для Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

- Чтобы развернуть веб-приложение Java в облаке, требуется подписка Azure. Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться для получения [бесплатной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [JDK Java 8](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)
- [Maven 3](http://maven.apache.org/)

## <a name="clone-the-sample-java-web-app-repository"></a>Клонирование примера репозитория для веб-приложения Java

В этом упражнении вы будете использовать приложение Spring Todo, которое представляет собой приложение Java, созданное с помощью [Spring Boot](https://spring.io/projects/spring-boot), [Spring Data для Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) и [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-introduction).
1. Клонируйте приложение Spring Todo и скопируйте содержимое папки **.prep** для инициализации проекта:

    Для Bash:
    ```bash
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    yes | cp -rf .prep/* .
    ```

    Для Windows:
    ```cmd
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    cd e2e-java-experience-in-app-service-linux-part-2
    xcopy .prep /f /s /e /y
    ```

2. Перейдите из каталога в следующую папку в клонированном репозитории:

   ```bash
   cd initial\spring-todo-app
   ```

## <a name="create-an-azure-cosmos-db-from-azure-cli"></a>Создание Azure Cosmos DB с помощью Azure CLI

Следующая процедура создает базу данных Azure Cosmos с помощью CLI.

1. Войдите в Azure CLI, а затем задайте идентификатор подписки.

    ```bash
    az login
    ```

2. При необходимости задайте идентификатор подписки.

    ```bash
    az account set -s <your-subscription-id>
    ```

3. Создайте группу ресурсов Azure и запишите ее имя для дальнейшего использования.

    ```bash
    az group create -n <your-azure-group-name> \
    -l <your-resource-group-region>
    ```

4. Создайте Cosmos DB и укажите тип GlobalDocumentDB.
В имени Cosmos DB нужно использовать только строчные буквы. Обязательно запомните значение поля `documentEndpoint` в ответе. Оно потребуется позже.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
     ```

5. Получите ключи Azure Cosmos DB и запишите значение `primaryMasterKey` для дальнейшего использования.

    ```bash
    az cosmosdb keys list -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

Следующая процедура запускает приложение на компьютере разработчика.

1. В предпочитаемой консоли настройте переменные среды, показанные в следующих разделах кода, используя сведения о подключении Azure и Cosmos DB, собранные ранее в этой статье. Необходимо указать уникальное имя для **WEBAPP_NAME** и значение для переменных **REGION**.

Для Linux (Bash):

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Для Windows (командная строка):
```cmd
set COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
set COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
set COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
set RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
set WEBAPP_NAME=<put-your-Webapp-name-here>
set REGION=<put-your-REGION-here>
```

> [!NOTE]
> Если вы хотите подготовить эти переменные с использованием скрипта, в каталоге .prep есть шаблон для Bash, который вы можете скопировать и использовать в качестве начальной точки.

2. Перейдите в следующий каталог:

    ```bash
    cd initial/spring-todo-app
    ``` 
 
3. Запустите приложение Spring Todo локально с помощью следующей команды:

    ```bash
    mvn package spring-boot:run
    ```

4. После запуска приложения вы можете проверить развертывание путем получения доступа к приложению Spring Todo здесь: [http://localhost:8080/](http://localhost:8080/).

 ![Приложение Spring, запущенное локально][SCDB01]

## <a name="deploy-to-app-service-linux"></a>Развертывание в Службу приложений (Linux)

Следующая процедура развертывает приложение в Linux в Azure.

1. Откройте файл pom.xml, скопированный ранее в каталог **initial/spring-todo-app** репозитория. Убедитесь, что [модуль Maven для Службы приложений Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) включен, как показано в файле pom.xml ниже. Если не задана версия **1.6.0**, обновите значение.

```xml
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.6.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

2. Развертывание в Java SE в Службе приложений (Linux)

    ```bash
    mvn azure-webapp:deploy
    ```

```bash
// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.6.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesnt exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlan11111111-1111-1111'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

3. Перейдите к веб-приложению, запущенному в Java SE в Службе приложений для Linux:

    ```bash
    https://<WEBAPP_NAME>.azurewebsites.net
    ```

![Приложение Spring, работающее в Службе приложений в Linux][SCDB02]

## <a name="troubleshoot-spring-todo-app-on-azure-by-viewing-logs"></a>Устранение неполадок приложения Spring Todo в Azure путем просмотра журналов

Следующая процедура открывает файлы журналов в Azure.

1. Настройте журналы для развернутого веб-приложения Java в Службе приложений Azure в Linux:

    ```bash
    az webapp log config --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME} \
     --web-server-logging filesystem
    ```
2. Откройте потоковую передачу удаленных журналов веб-приложения Java из локального компьютера:

    ```bash
    az webapp log tail --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME}
     ```

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers needs non existent service dev
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======


```

3. После завершения вы можете проверить результаты относительно кода в [e2e-java-experience-in-app-service-linux-part-2/complete](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/complete).


## <a name="scale-out-the-spring-todo-app"></a>Масштабирование приложения Spring Todo

Используйте следующую процедуру для масштабирования приложения.

1. Выполните масштабирование веб-приложения Java с помощью Azure CLI:

    ```bash
    az appservice plan update --number-of-workers 2 \
      --name ${WEBAPP_PLAN_NAME} \
      --resource-group ${RESOURCEGROUP_NAME}
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство разработчика для Java для службы приложений в Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-java).
- [Azure для разработчиков Java](https://docs.microsoft.com/azure/java/). Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Azure Cosmos DB Documentation]: /azure/cosmos-db/
[Azure для разработчиков Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data for Azure Cosmos DB SQL API]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Boot Document DB Starter for Azure]:https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: https://azure.microsoft.com/services/devops/java/ (Работа с Azure DevOps и Java)
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SCDB01]: ./media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB01.png
[SCDB02]: ./media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB02.png
