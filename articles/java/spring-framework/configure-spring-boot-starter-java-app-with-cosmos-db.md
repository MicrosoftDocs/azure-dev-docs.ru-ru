---
title: Использование начального приложения Spring Boot с API SQL Azure Cosmos DB
description: Сведения о настройке приложения, созданного с помощью Spring Boot Initializer в API Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: KarlErickson
ms.author: karler
ms.date: 10/02/2019
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 800e4d45417c7842d12e92c6e18dfdd6a1bc567f
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586191"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Использование начального приложения Spring Boot с API SQL Azure Cosmos DB

Azure Cosmos DB — это глобально распределенная служба баз данных, предоставляющая разработчикам возможность работать с данными с помощью разных стандартных API, например SQL, MongoDB, Graph и табличных API. Начальное приложение Spring Boot от Майкрософт позволяет разработчикам использовать приложения Spring Boot, которые легко интегрируются с Azure Cosmos DB с помощью API SQL.

В этой статье описано, как создать Azure Cosmos DB на портале Azure и пользовательское приложение Spring Boot с помощью **[Spring Initializr]** , а затем добавить [Начальное приложение Spring Boot CosmosDB для Azure] в пользовательское приложение для хранения и получения данных в Azure Cosmos DB с помощью Spring Data и API SQL Cosmos DB.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Создание Azure Cosmos DB с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и щелкните **Создать ресурс**.

1. Щелкните **Базы данных** и **Azure Cosmos DB**.

    ![Портал Azure][AZ02]

1. На странице **Azure Cosmos DB** введите следующие сведения.

    * Выберите **подписку**, которую нужно использовать для базы данных.
    * Укажите, следует ли создать **группу ресурсов** для базы данных, или выберите имеющуюся группу ресурсов.
    * Введите уникальное **имя учетной записи**, которое будет использоваться в качестве URI для базы данных, например: *wingtiptoysdata*.
    * Выберите **Core (SQL)** для API.
    * Укажите **расположение** для базы данных.

    Указав эти параметры, щелкните **Просмотреть и создать**, чтобы проверить спецификации. Затем щелкните **Создать**.

    ![Портал Azure][AZ03]

1. При создании база данных указывается на **панели мониторинга** Azure, а также на страницах **Все ресурсы** и **Azure Cosmos DB**. Вы можете выбрать свою базу данных в любом из этих расположений, чтобы открыть страницу свойств кэша.

1. Когда откроется страница свойств базы данных, щелкните **Ключи** и скопируйте URI и ключи доступа для базы данных. Эти значения будут использоваться в приложении Spring Boot.

    ![Портал Azure][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

Выполните описанные ниже действия, чтобы создать проект приложения Spring Boot с использованием поддержки Azure. В качестве альтернативы можно использовать пример [azure-spring-boot-sample-cosmosdb](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-cosmos), размещенный в репозитории [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java). В таком случае вы можете сразу перейти к разделу [Создание и тестирование приложения](#build-and-test-your-app).

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что вы хотите создать проект **Maven** на **Java**, выберите версию **Spring Boot**, введите имя **группы** и **артефакта** для приложения, добавьте **поддержку Azure** в зависимости и нажмите кнопку **Создать проект**.

    ![Основные параметры Spring Initializr][SI01]

    > [!NOTE]
    > Spring Initializr использует имена **группы** и **артефакта**, чтобы создать имя пакета, например *com.example.wingtiptoysdata*.

1. При появлении запроса скачайте проект на локальный компьютер и извлеките файлы.

Теперь вы можете вносить изменения в свое простое приложение Spring Boot.

## <a name="configure-your-spring-boot-application-to-use-the-azure-spring-boot-starter"></a>Настройка приложения Spring Boot для использования начального приложения Azure Spring Boot

1. Найдите файл *pom.xml* в каталоге приложения, например:

    `C:\SpringBoot\wingtiptoysdata\pom.xml`

    -или-

    `/users/example/home/wingtiptoysdata/pom.xml`

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте следующие строки в элемент `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.3.3</version>
    </dependency>
    ```

1. Убедитесь, что в элементе *properties* указаны требуемые версии Java и Azure:

    ```xml
    <properties>
       <java.version>1.8</java.version>
       <azure.version>2.2.0</azure.version>
    </properties>
    ```

1. Сохраните и закройте файл *pom.xml*.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Настройка приложения Spring Boot для использования с Azure Cosmos DB

1. Найдите файл *application.properties* в каталоге *resources*, например:

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    -или-

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. Откройте файл *application.properties* в текстовом редакторе, добавьте указанные ниже строки в файл и замените примеры значений на соответствующие свойства для базы данных:

    ```text
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmosdb.uri=https://wingtiptoys.documents.azure.com:443/

    # Specify the access key for your database.
    azure.cosmosdb.key=57686f6120447564652c20426f6220526f636b73==

    # Specify the name of your database.
    azure.cosmosdb.database=wingtiptoysdata
    ```

1. Сохраните и закройте файл *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Добавление примера кода для реализации базовых возможностей базы данных

В этом разделе мы создадим два класса Java для хранения пользовательских данных, а затем изменим класс основного приложения для создания экземпляра класса *User* и сохраним его в базе данных.

### <a name="define-a-base-class-for-storing-user-data"></a>Определение базового класса для хранения пользовательских данных

1. Создайте файл с именем *User.java* в том же каталоге, что и файл основного приложения Java.

1. Откройте файл *User.java* в текстовом редакторе и добавьте в него следующие строки, чтобы определить универсальный класс пользователя, позволяющего сохранять и извлекать значения в базе данных:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document;
    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;

    @Document(collection = "mycollection")
    public class User {

        @Id
        private String id;
        private String firstName;

        @PartitionKey
        private String lastName;
        private String address;

        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }

        public User() {
        }

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getFirstName() {
            return firstName;
        }

        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }

        public String getLastName() {
            return lastName;
        }

        public void setLastName(String lastName) {
            this.lastName = lastName;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return String.format("%s %s, %s", firstName, lastName, address);
        }
    }
    ```

1. Сохраните и закройте файл *User.java*.

### <a name="define-a-data-repository-interface"></a>Определение интерфейса хранилища данных

1. Создайте файл с именем *UserRepository.java* в том же каталоге, что и основной файл приложения Java.

1. Откройте файл *UserRepository.java* в текстовом редакторе и добавьте в него следующие строки, чтобы определить интерфейс пользовательского репозитория, дополняющего интерфейс `ReactiveCosmosRepository` по умолчанию:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.repository.ReactiveCosmosRepository;
    import org.springframework.stereotype.Repository;
    import reactor.core.publisher.Flux;

    @Repository
    public interface UserRepository extends ReactiveCosmosRepository<User, String> {
        Flux<User> findByFirstName(String firstName);
    }
    ```

    Интерфейс `ReactiveCosmosRepository` заменяет интерфейс `DocumentDbRepository` из предыдущей версии начального приложения. Новый интерфейс предоставляет синхронные и реактивные API для базовых операций сохранения, удаления и поиска.

1. Сохраните и закройте файл *UserRepository.java*.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

    `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

    -или-

    `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки:

    ```java
    package com.example.wingtiptoysdata;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
    import java.util.Optional;

    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {

        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);

        @Autowired
        private UserRepository repository;

        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }

        public void run(String... var1) throws Exception {
            final User testUser = new User("1", "Tasha", "Calderon", "4567 Main St Buffalo, NY 98052");

            LOGGER.info("Saving user: {}", testUser);

            // Save the User class to Azure CosmosDB database.
            final Mono<User> saveUserMono = repository.save(testUser);

            final Flux<User> firstNameUserFlux = repository.findByFirstName("testFirstName");

            //  Nothing happens until we subscribe to these Monos.
            //  findById will not return the user as user is not present.
            final Mono<User> findByIdMono = repository.findById(testUser.getId());
            final User findByIdUser = findByIdMono.block();
            Assert.isNull(findByIdUser, "User must be null");

            final User savedUser = saveUserMono.block();
            Assert.state(savedUser != null, "Saved user must not be null");
            Assert.state(savedUser.getFirstName().equals(testUser.getFirstName()), "Saved user first name doesn't match");

            LOGGER.info("Saved user");

            firstNameUserFlux.collectList().block();

            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");

            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");

            LOGGER.info("Found user by findById : {}", result);
        }

        @PostConstruct
        public void setup() {
            LOGGER.info("Clear the database");
            this.repository.deleteAll().block();
        }

        @PreDestroy
        public void cleanup() {
            LOGGER.info("Cleaning up users");
            this.repository.deleteAll().block();
        }
    }
    ```

1. Сохраните и закройте файл основного приложения Java.

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Откройте командную строку и перейдите к папке с файлом *pom.xml*, например:

    `cd C:\SpringBoot\wingtiptoysdata`

    -или-

    `cd /users/example/home/wingtiptoysdata`

1. Выполните сборку приложения и запустите его с помощью следующей команды:

    ```console
    mvnw clean test
    ```

    Эта команда позволяет автоматически запустить приложение как часть этапа тестирования. Кроме того, можно использовать следующую команду:

    ```console
    mvnw clean spring-boot:run
    ```

    После определенных выходных данных сборки и тестирования в окне консоли отобразится примерно такое сообщение:

    ```console
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::            (v2.2.0.RC1)
    >
    > 2019-10-04 15:19:06.817  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Starting WingtiptoysdataApplicationTests on devmachine03 with PID 30013 (started by <user> in /d/source/repos/wingtiptoysdata)
    > 2019-10-04 15:19:06.818  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : No active profile set, falling back to default profiles: default
    > 2019-10-04 15:19:08.329  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.720  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1369ms. Found 1 repository interfaces.
    > 2019-10-04 15:19:09.734  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.748  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 13ms. Found 0 repository interfaces.

    ... (omitting Cosmos DB connection output) ...

    > 2019-10-04 15:19:46.584  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Started WingtiptoysdataApplicationTests in 40.702 seconds (JVM running for 44.647)
    > 2019-10-04 15:19:46.587  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saving user: Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > 2019-10-04 15:19:47.122  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saved user
    > 2019-10-04 15:19:47.289  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Found user by findById : Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 44.003 s - in com.example.wingtiptoysdata.WingtiptoysdataApplicationTests
    > 2019-10-04 15:19:48.124  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.194  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.200  INFO 30013 --- [extShutdownHook] c.e.w.WingtiptoysdataApplication         : Cleaning up users
    > [INFO]
    > [INFO] Results:
    > [INFO]
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    > [INFO]
    > [INFO]
    > [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ wingtiptoysdata ---
    > [INFO] Building jar: /d/source/repos/wingtiptoysdata/target/wingtiptoysdata-0.0.1-SNAPSHOT.jar
    > [INFO]
    > [INFO] --- spring-boot-maven-plugin:2.2.0.RC1:repackage (repackage) @ wingtiptoysdata ---
    > [INFO] Replacing main artifact with repackaged archive
    > [INFO] ------------------------------------------------------------------------
    > [INFO] BUILD SUCCESS
    > [INFO] ------------------------------------------------------------------------
    > [INFO] Total time:  02:18 min
    > [INFO] Finished at: 2019-10-04T15:20:05-07:00
    > [INFO] ------------------------------------------------------------------------
    ```

    ![Успешные результаты приложения][JV02]

    Сообщения `Saved user` и `Found user` указывают на то, что данные были успешно сохранены в Cosmos DB и затем снова извлечены.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение, обязательно удалите созданную ранее группу ресурсов, содержащую Cosmos DB. Это можно сделать на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Azure PowerShell и Java см. в следующих статьях:

* [Документация по Azure Cosmos DB]

* [Разработка Создание ресурсов учетной записи API SQL для Azure Cosmos DB и управление ими с помощью приложения Java][Build a SQL API app with Java]

* [Spring Data для API SQL для Azure Cosmos DB]

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Начальное приложение Spring Boot CosmosDB для Azure]

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Документация по Azure Cosmos DB]: /azure/cosmos-db/
[Azure для разработчиков Java]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java
[Spring Data для API SQL для Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Начальное приложение Spring Boot CosmosDB для Azure]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmos
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: https://azure.microsoft.com/services/devops/java/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ01.png
[AZ02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ04]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ04.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png

[JV02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/JV02.png
