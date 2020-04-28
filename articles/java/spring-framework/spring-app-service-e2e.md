---
title: Развертывание приложения Spring/Tomcat в Службе приложений с использованием Базы данных Azure для MySQL
description: Полное руководство по использованию Службы приложений Java с MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: 4424cdb3f8ee01a10f892d38b84a42c820e95266
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82026107"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Развертывание приложения Spring в Службе приложений с использованием MySQL

Из этого руководства вы узнаете, как создавать, настраивать, развертывать и масштабировать веб-приложения Java, а также устранять неполадки с ними в Службе приложений в Linux.

Это руководство основано на популярном примере приложения PetClinic. В рамках этого раздела вы локально проверите версию приложения, которая использует HSQLDB, а затем развернете ее в [Службе приложений Azure](/azure/app-service/containers). После этого вы настроите и развернете версию, которая использует [Базу данных Azure для MySQL](/azure/mysql). Наконец, вы узнаете, как получить доступ к журналам приложения и выполнить горизонтальное увеличение масштаба, увеличив число рабочих ролей, в которых выполняется приложение.

## <a name="prerequisites"></a>Предварительные требования

* [Azure CLI](/cli/azure/overview)
* [Java 8](http://java.oracle.com/);
* [Maven 3](http://maven.apache.org/)
* [Git](https://github.com/);
* [Tomcat 9](https://tomcat.apache.org/download-80.cgi)
* [CLI MySQL](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Получение примера

Чтобы приступить к работе с примером приложения, клонируйте и подготовьте исходный репозиторий с помощью следующих команд.

# <a name="bash"></a>[bash](#tab/bash)

```bash
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```
---


## <a name="build-and-run-the-hsqldb-sample-locally"></a>Сборка и запуск примера HSQLDB в локальной среде

Сначала мы протестируем пример локально, используя HSQLDB в качестве базы данных.

Создайте версию HSQLDB примера приложения.

``` azurecli
mvn package
```

Затем присвойте переменной среды TOMCAT_HOME расположение установки Tomcat.

# <a name="bash"></a>[bash](#tab/bash)

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$TOMCAT_HOME="<Tomcat install directory>"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set TOMCAT_HOME=<Tomcat install directory>
```
---

После этого обновите файл *pom.xml*, чтобы настроить Maven для развертывания с использованием файла WAR Tomcat. Добавьте следующий код XML в качестве дочернего узла существующего элемента `<plugins>`. При необходимости измените `1.7.11` на текущую версию [подключаемого модуля Cargo Maven 2](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.11</version>
    <configuration>
        <container>
            <containerId>tomcat9x</containerId>
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

```azurecli
mvn cargo:deploy
```

Затем запустите Tomcat.

# <a name="bash"></a>[bash](#tab/bash)

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
& $TOMCAT_HOME/bin/catalina.bat run
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
%TOMCAT_HOME%\bin\catalina.bat run
```
---

Теперь в браузере можно перейти по адресу [http://localhost:8080](http://localhost:8080), чтобы увидеть работающее приложение и оценить его работу. Когда все будет готово, нажмите клавиши CTRL+C в командной строке Bash, чтобы завершить работу Tomcat.

## <a name="deploy-to-azure-app-service"></a>Развертывание в Службе приложений Azure

Теперь, когда приложение протестировано локально, его можно развернуть в Azure.

Сначала настройте следующие переменные среды. Для `REGION` используйте `Central US` или другие регионы, которые можно найти [здесь](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=app-service).

# <a name="bash"></a>[bash](#tab/bash)

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$RESOURCEGROUP_NAME="<resource group>"
$WEBAPP_NAME="<web app>"
$WEBAPP_PLAN_NAME="$WEBAPP_NAME-appservice-plan"
$REGION="<region>"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCEGROUP_NAME=<resource group>
set WEBAPP_NAME=<web app>
set WEBAPP_PLAN_NAME=%WEBAPP_NAME%-appservice-plan
set REGION=<region>
```
---

Maven будет использовать эти значения для создания ресурсов Azure с предоставленными вами именами. С помощью переменных среды можно хранить секреты учетной записи отдельно от файлов проекта.

Затем обновите файл *pom.xml*, чтобы настроить Maven для развертывания в Azure. Добавьте следующий код XML после добавленного ранее элемента `<plugin>`. При необходимости измените `1.9.1` на текущую версию [подключаемого модуля Maven для Службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.1</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 9.0</webContainer>
        </runtime>
        <deployment>
            <resources>
                <resource>
                    <directory>${project.basedir}/target</directory>
                    <includes>
                        <include>*.war</include>
                    </includes>
                </resource>
             </resources>
         </deployment>
    </configuration>
</plugin>
```

Затем войдите в Azure.

```azurecli
az login
```

После этого разверните приложение в Службе приложений в Linux.

```azurecli
mvn azure-webapp:deploy
```

Теперь вы можете перейти по адресу `https://<app-name>.azurewebsites.net` (после замены `<app-name>`), чтобы увидеть работающее приложение.

## <a name="set-up-azure-database-for-mysql"></a>Настройка Базы данных Azure для MySQL

Теперь вместо HSQLDB будет использоваться MySQL. Вам нужно будет создать экземпляр MySQL Server в Azure и добавить базу данных, а затем обновить конфигурацию приложения, используя новые сведения о подключении к базе данных.

Сначала настройте следующие переменные среды для дальнейшего использования.

# <a name="bash"></a>[bash](#tab/bash)

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$MYSQL_SERVER_NAME="<server>"
$MYSQL_SERVER_FULL_NAME="$MYSQL_SERVER_NAME.mysql.database.azure.com"
$MYSQL_SERVER_ADMIN_LOGIN_NAME="<admin>"
$MYSQL_SERVER_ADMIN_PASSWORD="<password>"
$MYSQL_DATABASE_NAME="<database>"
$DOLLAR="$"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set MYSQL_SERVER_NAME=<server>
set MYSQL_SERVER_FULL_NAME=%MYSQL_SERVER_NAME%.mysql.database.azure.com
set MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
set MYSQL_SERVER_ADMIN_PASSWORD=<password>
set MYSQL_DATABASE_NAME=<database>
set DOLLAR=$
```
---

Затем создайте и инициализируйте сервер базы данных. Выполните [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up) для начальной настройки. Затем используйте [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set), чтобы увеличить время ожидания подключения и задать часовой пояс сервера.

# <a name="bash"></a>[bash](#tab/bash)

```bash
az extension add --name db-up

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
    --server ${MYSQL_SERVER_NAME} --value=-8:00
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az extension add --name db-up

az mysql up `
    --resource-group $RESOURCEGROUP_NAME `
    --server-name $MYSQL_SERVER_NAME `
    --database-name $MYSQL_DATABASE_NAME `
    --admin-user $MYSQL_SERVER_ADMIN_LOGIN_NAME `
    --admin-password $MYSQL_SERVER_ADMIN_PASSWORD

az mysql server configuration set --name wait_timeout `
    --resource-group $RESOURCEGROUP_NAME `
    --server $MYSQL_SERVER_NAME --value 2147483

az mysql server configuration set --name time_zone `
    --resource-group $RESOURCEGROUP_NAME `
    --server $MYSQL_SERVER_NAME --value=-8:00
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
az extension add --name db-up

az mysql up ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server-name %MYSQL_SERVER_NAME% ^
    --database-name %MYSQL_DATABASE_NAME% ^
    --admin-user %MYSQL_SERVER_ADMIN_LOGIN_NAME% ^
    --admin-password %MYSQL_SERVER_ADMIN_PASSWORD%

az mysql server configuration set --name wait_timeout ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server %MYSQL_SERVER_NAME% --value 2147483

az mysql server configuration set --name time_zone ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server %MYSQL_SERVER_NAME% --value=-8:00
```
---

Затем с помощью CLI MySQL подключитесь к базе данных в Azure.

# <a name="bash"></a>[bash](#tab/bash)

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME}@${MYSQL_SERVER_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
mysql -u $MYSQL_SERVER_ADMIN_LOGIN_NAME@$MYSQL_SERVER_NAME `
 -h $MYSQL_SERVER_FULL_NAME -P 3306 -p
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mysql -u %MYSQL_SERVER_ADMIN_LOGIN_NAME%@%MYSQL_SERVER_NAME% ^
 -h %MYSQL_SERVER_FULL_NAME% -P 3306 -p
```
---

В командной строке CLI MySQL выполните приведенную ниже команду, чтобы проверить, названа ли ваша база данных тем же именем, которое вы указали ранее для переменной среды `MYSQL_DATABASE_NAME`.

```console
show databases;
```

Теперь вы можете использовать MySQL.

## <a name="configure-the-app-for-mysql"></a>Настройка приложения для работы с MySQL

Теперь нужно добавить сведения о подключении к версии MySQL приложения и выполнить развертывание в Службе приложений.

Обновите файл *pom.xml*, чтобы включить MySQL в действующую конфигурацию. Удалите элемент `<activation>` из профиля HSQLDB и поместите его в профиль MySQL, как показано ниже. В оставшейся части фрагмента кода показана существующая конфигурация. Обратите внимание, как настроенные ранее переменные среды используются Maven для настройки доступа к MySQL.

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
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}@${DOLLAR}{MYSQL_SERVER_FULL_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Затем обновите файл *pom.xml*, чтобы настроить Maven для развертывания в Azure и использования MySQL. Добавьте следующий код XML после добавленного ранее элемента `<plugin>`. При необходимости измените `1.9.1` на текущую версию [подключаемого модуля Maven для Службы приложений Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.1</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 9.0</webContainer>
        </runtime>
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
        <deployment>
            <resources>
                <resource>
                    <directory>${project.basedir}/target</directory>
                    <includes>
                        <include>*.war</include>
                    </includes>
                </resource>
             </resources>
         </deployment>
    </configuration>
</plugin>
```

Затем выполните сборку приложения и протестируйте его локально, развернув и запустив с помощью Tomcat.


# <a name="bash"></a>[bash](#tab/bash)

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
mvn package
mvn cargo:deploy
& $TOMCAT_HOME/bin/catalina.bat run
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
mvn package
mvn cargo:deploy
%TOMCAT_HOME%\bin\catalina.bat run
```
---

Теперь приложение можно просмотреть локально, перейдя по адресу [http://localhost:8080](http://localhost:8080). Приложение будет выглядеть и работать так же, как и раньше, но теперь оно использует базу данных Azure для MySQL вместо HSQLDB. Когда все будет готово, нажмите клавиши CTRL+C в командной строке Bash, чтобы завершить работу Tomcat.

Наконец, разверните приложение в Службе приложений.

```bash
mvn azure-webapp:deploy
```

Теперь можно перейти по адресу `https://<app-name>.azurewebsites.net`, чтобы увидеть работающее приложение, использующее Службу приложений и Базу данных Azure для MySQL.

## <a name="access-the-app-logs"></a>Доступ к журналам приложения

Если необходимо устранить неполадки, можно просмотреть журналы приложения. Чтобы открыть удаленный поток журналов на локальном компьютере, используйте следующую команду.

# <a name="bash"></a>[bash](#tab/bash)

```bash
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az webapp log tail --name $WEBAPP_NAME `
    --resource-group $RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az webapp log tail --name %WEBAPP_NAME% ^
    --resource-group %RESOURCEGROUP_NAME%
```
---

Завершив просмотр журналов, нажмите клавиши CTRL+C, чтобы остановить поток.

Этот поток журналов также доступен по адресу `https://<app-name>.scm.azurewebsites.net/api/logstream`.

## <a name="scale-out"></a>Горизонтальное увеличение масштаба

Чтобы вы могли увеличить трафик в приложении, выполните горизонтальное увеличение масштаба до нескольких экземпляров с помощью следующей команды.

# <a name="bash"></a>[bash](#tab/bash)

```bash
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az appservice plan update --number-of-workers 2 `
    --name $WEBAPP_PLAN_NAME `
    --resource-group $RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az appservice plan update --number-of-workers 2 ^
    --name %WEBAPP_PLAN_NAME% ^
    --resource-group %RESOURCEGROUP_NAME%
```
---

Поздравляем! Вы создали и масштабировали веб-приложение Java, используя Spring Framework, JSP, Spring Data, Hibernate, JDBC, Службу приложений в Linux и Базу данных Azure для MySQL.

## <a name="clean-up-resources"></a>Очистка ресурсов

Ранее вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов, выполнив следующую команду.


# <a name="bash"></a>[bash](#tab/bash)

```bash
az group delete --name ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az group delete --name $RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az group delete --name %RESOURCEGROUP_NAME%
```
---

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с другими вариантами конфигурации и вариантами CI/CD, доступными для Java в Службе приложений.

> [!div class="nextstepaction"]
> [Настройка приложения Java в Linux для Службы приложений Azure](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Создание и развертывание веб-приложения Java с помощью Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Развертывание в Службе приложений Azure с помощью подключаемого модуля Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
