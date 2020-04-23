---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 6d6f0f701e9507f5dee065c14b48b30be423e100
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672850"
---
### <a name="create-a-docker-image-for-wildfly"></a>Создание образа Docker для WildFly

Чтобы создать Dockerfile, вам потребуется следующее:

* поддерживаемый JDK;
* установленная версия WildFly;
* среда выполнения JVM;
* способ передачи переменных среды (если применимо).

Затем можно выполнить действия, описанные в следующих разделах, там, где это применимо. В качестве отправной точки для работы с Dockerfile и веб-приложения можно использовать [репозиторий для быстрого начала работы с контейнерами WildFly](https://github.com/Azure/wildfly-container-quickstart).

1. [Настройка хранилища ключей FlexVolume](#configure-keyvault-flexvolume)
2. [Настройка источников данных](#set-up-data-sources)
3. [Настройка ресурсов JNDI](#set-up-jndi-resources)
4. [Проверка конфигурации WildFly](#review-wildfly-configuration)

#### <a name="configure-keyvault-flexvolume"></a>Настройка хранилища ключей FlexVolume

Создайте хранилище ключей в Azure и укажите все необходимые секреты. Дополнительные сведения см. в [кратком руководстве Настройка и получение секрета из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli). Затем настройте [хранилище ключей FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md), чтобы сделать эти секреты доступными для групп pod.

Кроме того, потребуется обновить скрипт запуска, используемый для начальной загрузки WildFly. Этот скрипт перед запуском сервера должен импортировать сертификаты в хранилище ключей, используемое WildFly.

#### <a name="set-up-data-sources"></a>настройка источников данных;

Чтобы настроить WildFly для доступа к источнику данных, необходимо добавить JAR-файл драйвера JDBC в образ Docker, а затем выполнить соответствующие команды интерфейса командной строки JBoss. Эти команды должны настроить источник данных при создании образа Docker.

Следующие шаги содержат инструкции для PostgreSQL, MySQL и SQL Server.

1. Загрузите драйвер JDBC для [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) или [SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    Распакуйте загруженный архив, чтобы получить JAR-файл драйвера.

1. Создайте файл (например, с именем `module.xml`), и добавьте следующую разметку. Замените заполнитель `<module name>` (включая угловые скобки) на `org.postgres` для PostgreSQL, на `com.mysql` для MySQL или на `com.microsoft` для SQL Server. Замените `<JDBC .jar file path>` именем JAR-файла из предыдущего шага, включая полный путь к расположению, в которое будет помещен файл в образе Docker, например в `/opt/database`.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Создайте файл (например, с именем `datasource-commands.cli`), и добавьте следующий код. Замените `<JDBC .jar file path>` значением, использованным на предыдущем шаге. Замените `<module file path>` именем файла и путем из предыдущего шага, например `/opt/database/module.xml`.

    **PostgreSQL**

    ```console
    batch
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)
    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    reload
    run batch
    shutdown
    ```

    **MySQL**

    ```console
    batch
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)
    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter
    reload
    run batch
    shutdown
    ```

    **SQL Server**

    ```console
    batch
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)
    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter
    reload
    run batch
    shutdown
    ```

1. Обновите конфигурацию источника данных JTA для приложения:

    Откройте файл `src/main/resources/META-INF/persistence.xml` для приложения и найдите элемент `<jta-data-source>`. Замените его содержимое, как показано ниже:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

1. Добавьте следующий код в `Dockerfile`, чтобы при сборке образа Docker создавался источник данных.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/database/datasource-commands.cli && \
    sleep 30
    ```

1. Определите используемые адреса `DATABASE_CONNECTION_URL`, так как они отличаются для каждого сервера базы данных и отличаются от указанных на портале Azure. Указанные здесь форматы URL-адресов являются обязательными при использовании WildFly:

    **PostgreSQL**

    ```console
    jdbc:postgresql://<database server name>:5432/<database name>?ssl=true
    ```

    **MySQL**

    ```console
    jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT
    ```

    **SQL Server**

    ```console
    jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

1. При создании развертывания YAML на более позднем этапе потребуется передать с соответствующими значениями следующие переменные среды: `DATABASE_CONNECTION_URL`, `DATABASE_SERVER_ADMIN_FULL_NAME` и `DATABASE_SERVER_ADMIN_PASSWORD`.

Дополнительные сведения о настройке подключения к базе данных с помощью WildFly см. в статьях для [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) или [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

#### <a name="set-up-jndi-resources"></a>Настройка ресурсов JNDI

Чтобы настроить каждый ресурс JNDI для WildFly, обычно необходимо выполнить следующие действия.

1. Загрузите необходимые JAR-файлы и скопируйте их в образ Docker.
2. Создайте файл WildFly *module.xml*, ссылающийся на эти JAR-файлы.
3. Создайте конфигурацию, необходимую для определенного ресурса JNDI.
4. Создайте скрипт командной строки JBoss, который будет использоваться во время сборки Docker для регистрации ресурса JNDI.
5. Добавьте все это в Dockerfile.
6. Передайте соответствующие переменные среды в развертывание YAML.

В приведенном ниже примере показаны шаги, необходимые для создания ресурса JNDI для подключения JMS к служебной шине Azure.

1. Загрузите [поставщик JMS для Apache Qpid](https://qpid.apache.org/components/jms/index.html)

    Распакуйте загруженный архив, чтобы получить JAR-файлы.

1. Создайте файл (например, с именем `module.xml`), и добавьте следующую разметку в `/opt/servicebus`. Убедитесь, что номера версий JAR-файлов совпадают с именами JAR-файлов в предыдущем шаге.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
     <resources>
      <resource-root path="proton-j-0.31.0.jar"/>
      <resource-root path="qpid-jms-client-0.40.0.jar"/>
      <resource-root path="slf4j-log4j12-1.7.25.jar"/>
      <resource-root path="slf4j-api-1.7.25.jar"/>
      <resource-root path="log4j-1.2.17.jar"/>
      <resource-root path="netty-buffer-4.1.32.Final.jar" />
      <resource-root path="netty-codec-4.1.32.Final.jar" />
      <resource-root path="netty-codec-http-4.1.32.Final.jar" />
      <resource-root path="netty-common-4.1.32.Final.jar" />
      <resource-root path="netty-handler-4.1.32.Final.jar" />
      <resource-root path="netty-resolver-4.1.32.Final.jar" />
      <resource-root path="netty-transport-4.1.32.Final.jar" />
      <resource-root path="netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar" />
      <resource-root path="netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar" />
      <resource-root path="netty-transport-native-unix-common-4.1.32.Final.jar" />
      <resource-root path="qpid-jms-discovery-0.40.0.jar" />
     </resources>
     <dependencies>
      <module name="javax.api"/>
      <module name="javax.jms.api"/>
     </dependencies>
    </module>
    ```

1. Создайте файл `jndi.properties` в `/opt/servicebus`.

    ```console
    connectionfactory.${MDB_CONNECTION_FACTORY}=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}
    queue.${MDB_QUEUE}=${SB_QUEUE}
    topic.${MDB_TOPIC}=${SB_TOPIC}
    ```

1. Создайте файл (например, с именем `servicebus-commands.cli`), и добавьте следующий код.

    ```console
    batch
    /subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)
    /system-property=property.mymdb.queue:add(value=myqueue)
    /system-property=property.connection.factory:add(value=java:global/remoteJMS/SBF)
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/opt/servicebus/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/opt/servicebus/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    run-batch
    reload
    shutdown
    ```

1. Добавьте следующий код в `Dockerfile`, чтобы при сборке образа Docker создавался источник JNDI.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/servicebus/servicebus-commands.cli && \
    sleep 30
    ```

1. При создании развертывания YAML на более позднем этапе потребуется передать с соответствующими значениями следующие переменные среды: `MDB_CONNECTION_FACTORY`, `DEFAULT_SBNAMESPACE`, `SB_SAS_POLICY`, `SB_SAS_KEY`, `MDB_QUEUE`, `SB_QUEUE`, `MDB_TOPIC` и `SB_TOPIC`.

#### <a name="review-wildfly-configuration"></a>Проверка конфигурации WildFly

Ознакомьтесь с [руководством по администрированию WildFly](https://docs.wildfly.org/18/Admin_Guide.html), чтобы узнать о дополнительных шагах, выполняемых перед миграцией, не описанных в предыдущем руководстве.
