---
title: Развертывание приложения Spring/Tomcat в Службе приложений с использованием Базы данных Azure для MySQL
description: Полное руководство по использованию Службы приложений Java с MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: aa26952b320392beb553f327920fe5bd905a0b85
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811824"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Развертывание приложения Spring в Службе приложений с использованием MySQL

Из этого руководства вы узнаете, как создавать, настраивать, развертывать и масштабировать веб-приложения Java, а также устранять неполадки с ними в Службе приложений в Linux.

Это руководство основано на популярном примере приложения PetClinic. В рамках этого раздела вы локально проверите версию приложения, которая использует HSQLDB, а затем развернете ее в [Службе приложений Azure](/azure/app-service/containers). После этого вы настроите и развернете версию, которая использует [Базу данных Azure для MySQL](/azure/mysql). Наконец, вы узнаете, как получить доступ к журналам приложения и выполнить масштабирование, увеличив число рабочих ролей, в которых выполняется приложение.

## <a name="prerequisites"></a>Предварительные требования

* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/overview)
* [Java 8](http://java.oracle.com/);
* [Maven 3](http://maven.apache.org/)
* [Git](https://github.com/)
* [Tomcat](https://tomcat.apache.org/download-80.cgi)
* [CLI MySQL](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Получение примера

Чтобы приступить к работе с примером приложения, клонируйте и подготовьте исходный репозиторий с помощью следующих команд.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux.git
cd e2e-java-experience-in-app-service-linux
yes | cp -rf .prep/* .
```

## <a name="build-and-run-the-hsqldb-sample-locally"></a>Сборка и запуск примера HSQLDB в локальной среде

Сначала мы протестируем пример локально, используя HSQLDB в качестве базы данных.

Перейдите к версии HSQLDB примера и выполните сборку.

```bash
cd initial-hsqldb/spring-framework-petclinic
mvn package
```

Затем присвойте переменной среды TOMCAT_HOME расположение установки Tomcat.

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

После этого обновите файл *pom.xml*, чтобы настроить Maven для развертывания с использованием файла WAR Tomcat. Добавьте следующий код XML в качестве дочернего узла существующего элемента `<plugins>`. При необходимости измените `1.7.7` на текущую версию [подключаемого модуля Cargo Maven 2](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.7</version>
    <configuration>
        <container>
            <containerId>tomcat8x</containerId>
            <type>installed</type>
            <home>${TOMCAT_HOME}</home>
        </container>
        <configuration>
            <type>existing</type>
            <home>${TOMCAT_HOME}</home>
        </configuration>
        <deployables>
            <deployable>
                <groupId>${project.groupId}</groupId>
                <artifactId>${project.artifactId}</artifactId>
                <type>war</type>
                <properties>
                    <context>/</context>
                </properties>
            </deployable>
        </deployables>
    </configuration>
</plugin>
```

В такой конфигурации приложение можно развернуть локально в Tomcat.

```bash
mvn cargo:deploy
```

Затем запустите Tomcat.

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

Теперь в браузере можно перейти по адресу [http://localhost:8080](http://localhost:8080), чтобы увидеть работающее приложение и оценить его работу. Когда все будет готово, нажмите клавиши CTRL+C в командной строке Bash, чтобы завершить работу Tomcat.

## <a name="deploy-to-azure-app-service"></a>Развертывание в Службе приложений Azure

Теперь, когда приложение протестировано локально, его можно развернуть в Azure.

Сначала настройте следующие переменные среды.

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

Maven будет использовать эти значения для создания ресурсов Azure с предоставленными вами именами. С помощью переменных среды можно хранить секреты учетной записи отдельно от файлов проекта.

Затем обновите файл *pom.xml*, чтобы настроить Maven для развертывания в Azure. Добавьте следующий код XML после добавленного ранее элемента `<plugin>`. При необходимости измените `1.7.0` на текущую версию [подключаемого модуля Maven для Службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.8.0</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 8.5</webContainer>
        </runtime>
    </configuration>
</plugin>
```

Затем войдите в Azure.

```azurecli
az login
```

После этого разверните приложение в Службе приложений в Linux.

```bash
mvn azure-webapp:deploy
```

Теперь вы можете перейти по адресу `https://<app-name>.azurewebsites.net` (после замены `<app-name>`), чтобы увидеть работающее приложение.

## <a name="set-up-azure-database-for-mysql"></a>Настройка Базы данных Azure для MySQL

Теперь вместо HSQLDB будет использоваться MySQL. Вам нужно будет создать экземпляр MySQL Server в Azure и добавить базу данных, а затем обновить конфигурацию приложения, используя новые сведения о подключении к базе данных.

Сначала настройте следующие переменные среды для дальнейшего использования.

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

Затем создайте и инициализируйте сервер базы данных. Выполните [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up) для начальной настройки. Затем используйте [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set), чтобы увеличить время ожидания подключения и задать часовой пояс сервера.

```azurecli
az mysql up \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server-name ${MYSQL_SERVER_NAME} \
    --database-name ${MYSQL_DATABASE_NAME} \
    --admin-user ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
    --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD}

az mysql server configuration set --name wait_timeout \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value 2147483

az mysql server configuration set --name time_zone \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value -8:00
```

После этого с помощью CLI MySQL создайте базу данных.

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

В командной строке CLI MySQL выполните следующую команду, заменив `<database name>` тем же значением, которое вы указали ранее для переменной среды `MYSQL_DATABASE_NAME`.

```console
CREATE DATABASE <database name>;
```

Теперь вы можете использовать MySQL.

## <a name="configure-the-app-for-mysql"></a>Настройка приложения для работы с MySQL

Теперь нужно добавить сведения о подключении к версии MySQL приложения и выполнить развертывание в Службе приложений.

Сначала перейдите в нужную папку в командной строке Bash.

```bash
cd ../../initial-mysql/spring-framework-petclinic
```

Затем обновите файл *pom.xml*, чтобы включить MySQL в действующую конфигурацию. Удалите элемент `<activation>` из профиля HSQLDB и поместите его в профиль MySQL, как показано ниже. В оставшейся части фрагмента кода показана существующая конфигурация. Обратите внимание, как настроенные ранее переменные среды используются Maven для настройки доступа к MySQL.

```xml
<profile>
    <id>MySQL</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <db.script>mysql</db.script>
        <jpa.database>MYSQL</jpa.database>
        <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
        <jdbc.url>jdbc:mysql://${DOLLAR}{MYSQL_SERVER_FULL_NAME}:3306/${DOLLAR}{MYSQL_DATABASE_NAME}?useUnicode=true</jdbc.url>
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Затем обновите файл *pom.xml*, чтобы настроить Maven для развертывания в Azure и использования MySQL. Добавьте следующий код XML после добавленного ранее элемента `<plugin>`. При необходимости измените `1.7.0` на текущую версию [подключаемого модуля Maven для Службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.7.0</version>
    <configuration>

        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>

        <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>

        <appSettings>
            <property>
                <name>MYSQL_SERVER_FULL_NAME</name>
                <value>${MYSQL_SERVER_FULL_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_LOGIN_NAME</name>
                <value>${MYSQL_SERVER_ADMIN_LOGIN_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_PASSWORD</name>
                <value>${MYSQL_SERVER_ADMIN_PASSWORD}</value>
            </property>
            <property>
                <name>MYSQL_DATABASE_NAME</name>
                <value>${MYSQL_DATABASE_NAME}</value>
            </property>
        </appSettings>

    </configuration>
</plugin>
```

Затем выполните сборку приложения и протестируйте его локально, развернув и запустив с помощью Tomcat.

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

Теперь приложение можно просмотреть локально, перейдя по адресу [http://localhost:8080](http://localhost:8080). Приложение будет выглядеть и работать так же, как и раньше, но теперь оно использует базу данных Azure для MySQL вместо HSQLDB. Когда все будет готово, нажмите клавиши CTRL+C в командной строке Bash, чтобы завершить работу Tomcat.

Наконец, разверните приложение в Службе приложений.

```bash
mvn azure-webapp:deploy
```

Теперь можно перейти по адресу `https://<app-name>.azurewebsites.net`, чтобы увидеть работающее приложение, использующее Службу приложений и Базу данных Azure для MySQL.

## <a name="access-the-app-logs"></a>Доступ к журналам приложения

Если необходимо устранить неполадки, можно просмотреть журналы приложения. Чтобы открыть удаленный поток журналов на локальном компьютере, используйте следующую команду.

```azurecli
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Завершив просмотр журналов, нажмите клавиши CTRL+C, чтобы остановить поток.

Этот поток журналов также доступен по адресу `https://<app-name>.scm.azurewebsites.net/api/logstream`.

## <a name="scale-out"></a>Масштабирование

Чтобы вы могли увеличить трафик в приложении, выполните масштабирование до нескольких экземпляров с помощью следующей команды.

```azurecli
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Поздравляем! Вы создали и масштабировали веб-приложение Java, используя Spring Framework, JSP, Spring Data, Hibernate, JDBC, Службу приложений в Linux и Базу данных Azure для MySQL.

## <a name="clean-up-resources"></a>Очистка ресурсов

Ранее вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов, выполнив следующую команду.

```azurecli
az group delete --name ${RESOURCEGROUP_NAME}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с другими вариантами конфигурации и вариантами CI/CD, доступными для Java в Службе приложений.

> [!div class="nextstepaction"]
> [Настройка приложения Java в Linux для Службы приложений Azure](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Создание и развертывание веб-приложения Java с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Развертывание в Службе приложений Azure с помощью подключаемого модуля Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
