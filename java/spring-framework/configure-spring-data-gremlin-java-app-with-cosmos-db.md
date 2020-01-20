---
title: Использование начального приложения Spring Data Gremlin с API SQL Azure Cosmos DB
description: Сведения о настройке приложения, созданного с помощью Spring Boot Initializer в API Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 01/10/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: 61bf7d78edf2fcdc755d90588fe1c839d319f823
ms.sourcegitcommit: ac68fb174d606c7af2bfa79fe32b8ca7b73c86a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946775"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Использование начального приложения Spring Data Gremlin с API SQL Azure Cosmos DB

## <a name="overview"></a>Обзор

Начальное приложение Spring Data Gremlin обеспечивает поддержку Spring Data для языка запросов Gremlin от Apache, который разработчики могут использовать с любым хранилищем данных, совместимым с Gremlin.

В этой статье демонстрируется создание базы данных Azure Cosmos DB на портале Azure для использования с API Gremlin, создание пользовательского приложения Java с помощью **[Spring Initializr]** , а затем добавление в это приложение функций начального приложения Spring Data Gremlin для хранения данных в Azure Cosmos DB и получения этих данных с помощью языка запросов Gremlin.

## <a name="prerequisites"></a>предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

> [!IMPORTANT]
>
> Для выполнения операций, описанных в этой статье, требуется Spring Boot 2.0 или более поздней версии.
>

## <a name="create-an-azure-cosmos-db-using-the-azure-portal"></a>Создание базы данных Azure Cosmos DB с помощью портала Azure

### <a name="create-your-azure-cosmos-database-for-use-with-gremlin-api"></a>Создание базы данных Azure Cosmos DB для использования с API Gremlin

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и щелкните **+Создать ресурс**.

1. Щелкните **Базы данных** и **Azure Cosmos DB**.

   ![Создание Azure Cosmos DB][AZ02]

1. На странице **Azure Cosmos DB** введите следующие сведения.

   * Выберите **подписку** для использования с базой данных.
   * Укажите, нужно ли создать **группу ресурсов** для базы данных, или выберите существующую группу ресурсов.
   * Введите уникальное **имя учетной записи**, которое будет использоваться как элемент универсального кода ресурса (URI) Gremlin для вашей базы данных. Например, если указать **wingtiptoysdata** в качестве **имени учетной записи**, URI Gremlin будет *wingtiptoysdata.gremlin.cosmosdb.azure.com*.
   * В поле "API" выберите **Gremlin (Graph)** .
   * Укажите **расположение** для базы данных.
   
Указав эти параметры, щелкните **Просмотр и создание**.

   ![Указание параметров Azure Cosmos DB][AZ03]

Проверьте спецификацию и щелкните **Создать**, чтобы создать базу данных.

1. Создав базу данных, щелкните **Перейти к ресурсу**. База данных отобразится на **панели мониторинга** Azure, а также на страницах **Все ресурсы** и **Azure Cosmos DB**. Вы можете выбрать свою базу данных в любом из этих расположений, чтобы открыть страницу свойств кэша.

1. Когда откроется страница свойств базы данных, щелкните **Ключи** и скопируйте URI и ключи доступа для базы данных. Эти значения будут использоваться в приложении Spring Boot.

   ![Ключи доступа][AZ05]

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Добавление графа в базу данных Azure Cosmos DB

1. Щелкните **Обозреватель данных**, а затем выберите **Добавление графа**.

   ![Добавление графа][AZ06]

1. В окне **Добавление графа** добавьте следующие сведения:

   * Укажите уникальное значение в поле **Идентификатор базы данных**.
   * Можно указать свое значение в поле **Емкость хранилища** или оставить значение по умолчанию.
   * Укажите уникальное значение в поле **Идентификатор графа**.
   * Укажите **Ключ секции**. См. сведения в руководстве по [использованию секционированного графа в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).
Нажмите кнопку **ОК**.
   
   Указав эти параметры, щелкните **ОК**, чтобы создать граф.

   ![Добавление графа][AZ07]

1. После создания графа его можно просмотреть при помощи **обозревателя данных**.

   ![Окно свойств графа][AZ08]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что требуется создать проект **Maven** на **Java**, введите имена **группы** и **артефакта** для приложения, укажите версию **Spring Boot** (необходима версия 2.0 или более поздняя версия), а затем щелкните **Создать проект**.

   ![Основные параметры Spring Initializr][SI01]

   > [!NOTE]
   >
   > Spring Initializr использует имена **группы** и **артефакта**, чтобы создать имя пакета, например *com.example.wintiptoysdata.
   >

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать.

   ![Пользовательские файлы проекта Spring Boot][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Настройка приложения Spring Boot для использования начального приложения Spring Data Gremlin

1. Найдите файл *pom.xml* в каталоге приложения, например:

   `C:\SpringBoot\wingtiptoysdata\pom.xml`

   -или-

   `/users/example/home/wingtiptoysdata/pom.xml`

1. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Data Gremlin в список `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.spring.data.gremlin</groupId>
      <artifactId>spring-data-gremlin</artifactId>
      <version>2.0.0</version>
   </dependency>
   ```

   ![Изменение файла pom.xml][PM02]

1. Сохраните и закройте файл *pom.xml*.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Настройка приложения Spring Boot для использования Azure Cosmos DB

1. Найдите каталог *resources* своего приложения и создайте файл с именем *application.yml*. Пример:

   `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.yml`

   -или-

   `/users/example/home/wingtiptoysdata/src/main/resources/application.yml`

   ![Создание файла application.yml][RE01]

1. Откройте файл *application.yml* в текстовом редакторе, добавьте указанные ниже строки в файл и замените примеры значений соответствующими свойствами своей базы данных:

   ```yaml
   gremlin:
      endpoint: wingtiptoys.gremlin.cosmosdb.azure.com
      port: 443
      username: /dbs/wingtiptoysdb/colls/wingtiptoysgraph
      password: 57686f6120447564652c20426f6220526f636b73==
      telemetryAllowed: false
   ```
   
   Где:
   
   | Поле | Description |
   |---|---|
   | `endpoint` | Указывает URI Gremlin, который создается на основе уникального **ИД**, введенного при создании базы данных Azure Cosmos DB ранее в этом руководстве. |
   | `port` | Указывает порт TCP/IP, который для протокола HTTPS должен иметь значение **443**. |
   | `username` | Указывает уникальные значения параметров **Идентификатор базы данных** и **Идентификатор графа**, которые использовались при добавлении графа ранее в этом руководстве. Это значение нужно вводить, используя следующий синтаксис: "/dbs/ **{Идентификатор базы данных}** /colls/ **{Идентификатор графа}** ". |
   | `password` | Указывает основной или дополнительный **ключ доступа**, который вы скопировали ранее в этом руководстве. |
   | `telemetryAllowed` | Укажите **true**, если нужно включить телеметрию. В противном случае укажите **false**.

1. Сохраните и закройте файл *application.yml*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Добавление примера кода для реализации базовых возможностей базы данных

В этом разделе вы создадите классы Java, необходимые для хранения данных в созданной базе данных.

### <a name="modify-the-main-application-class"></a>Изменение класса основного приложения

1. Найдите файл основного приложения Java в каталоге пакета приложения, например:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

   -или-

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

   ![Поиск файла приложения Java][JV01]

1. Откройте файл основного приложения Java в текстовом редакторе и добавьте в него следующие строки:

   ```java
   package com.example.wingtiptoysdata;
   
   // These imports are required for the application.
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.example.wingtiptoysdata.domain.Network;
   import com.example.wingtiptoysdata.domain.Person;
   import com.example.wingtiptoysdata.domain.Relation;
   import com.example.wingtiptoysdata.repository.NetworkRepository;
   import com.example.wingtiptoysdata.repository.PersonRepository;
   import com.example.wingtiptoysdata.repository.RelationRepository;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import javax.annotation.PostConstruct;
   import javax.annotation.PreDestroy;
   
   @SpringBootApplication
   public class WingtiptoysdataApplication {
   
       // Define several person classes to store personal data.
       private final Person person1 = new Person("01", "Nellie Hughes", "23");
       private final Person person2 = new Person("02", "Delmar Alfred", "34");
       private final Person person3 = new Person("03", "Kelley Hunter", "45");
       private final Person person4 = new Person("04", "Roscoe Guerin", "56");
       private final Person person5 = new Person("05", "Gracie Chavez", "67");
   
       // Define relationship classes to define the relationships between some of the persons.
       private final Relation relation1 = new Relation("0102", "siblings", person1, person2);
       private final Relation relation2 = new Relation("0203", "coworkers", person2, person3);
       private final Relation relation3 = new Relation("0301", "parent", person3, person1);
       private final Relation relation4 = new Relation("0302", "parent", person3, person2);
       private final Relation relation5 = new Relation("0501", "grandparent", person5, person1);
       private final Relation relation6 = new Relation("0502", "grandparent", person5, person2);
   
       // Define the network.
       private final Network network = new Network();
   
       // Autowire the repositories and factory.
       @Autowired
       private PersonRepository personRepo;
       @Autowired
       private RelationRepository relationRepo;
       @Autowired
       private NetworkRepository networkRepo;
       @Autowired
       private GremlinFactory factory;
   
       // Run the Spring application and exit.
    public static void main(String[] args) {
           SpringApplication.run(WingtiptoysdataApplication.class, args);
           System.exit(0);
    }
   
       // Perform post-construct operations.    
       @PostConstruct
       public void setup() {
           // Delete any existing data from the database.
           this.networkRepo.deleteAll();
   
           // Add the relationship classes as edges.
           this.network.getEdges().add(this.relation1);
           this.network.getEdges().add(this.relation2);
           this.network.getEdges().add(this.relation3);
           this.network.getEdges().add(this.relation4);
           this.network.getEdges().add(this.relation5);
           this.network.getEdges().add(this.relation6);
   
           // Add the person classes as vertices.
           this.network.getVertexes().add(this.person1);
           this.network.getVertexes().add(this.person2);
           this.network.getVertexes().add(this.person3);
           this.network.getVertexes().add(this.person4);
           this.network.getVertexes().add(this.person5);
   
           // Save the network.
           this.networkRepo.save(this.network);
       }
   }
   ```

1. Сохраните и закройте файл основного приложения Java.

### <a name="define-a-basic-class-for-storing-configuration-information"></a>Создание простого класса для хранения сведений о конфигурации

1. В каталоге пакета своего приложения создайте папку с именем *config*, например:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\config`

   -или-

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/config`

1. В каталоге *config* создайте файл Java с именем *UserRepositoryConfiguration.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.config;

   import com.microsoft.spring.data.gremlin.common.GremlinConfiguration;
   import com.microsoft.spring.data.gremlin.common.GremlinFactory;
   import com.microsoft.spring.data.gremlin.config.AbstractGremlinConfiguration;
   import com.microsoft.spring.data.gremlin.repository.config.EnableGremlinRepositories;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.PropertySource;
   
   @Configuration
   @EnableGremlinRepositories(basePackages = "com.example.wingtiptoysdata.repository")
   @EnableConfigurationProperties(GremlinConfiguration.class)
   @PropertySource("classpath:application.yml")
   public class UserRepositoryConfiguration extends AbstractGremlinConfiguration {
   
       @Autowired
       private GremlinConfiguration config;
   
       @Override
       public GremlinConfiguration getGremlinConfiguration() {
           return this.config;
       }
   }
   ```

1. Сохраните и закройте файл *UserRepositoryConfiguration.java*.

### <a name="define-a-set-of-classes-that-define-the-elements-of-your-graph-database"></a>Создание набора классов, которые определяют элементы графовой базы данных

1. В каталоге пакета своего приложения создайте папку с именем *domain*, например:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\domain`

   -или-

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/domain`

1. В каталоге *domain* создайте файл Java с именем *Person.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Vertex;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Vertex
   @AllArgsConstructor
   @NoArgsConstructor
   public class Person {
   
       @Id
       private String id;
   
       private String name;
   
       private String age;
   }
   ```

1. Сохраните и закройте файл *Person.java*.

1. В каталоге *domain* создайте файл Java с именем *Relation.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.Edge;
   import com.microsoft.spring.data.gremlin.annotation.EdgeFrom;
   import com.microsoft.spring.data.gremlin.annotation.EdgeTo;
   import lombok.AllArgsConstructor;
   import lombok.Data;
   import lombok.NoArgsConstructor;
   import org.springframework.data.annotation.Id;
   
   @Data
   @Edge
   @AllArgsConstructor
   @NoArgsConstructor
   public class Relation {
   
       @Id
       private String id;
   
       private String name;
   
       @EdgeFrom
       private Person personFrom;
   
       @EdgeTo
       private Person personTo;
   }
   ```

1. Сохраните и закройте файл *Relation.java*.

1. В каталоге *domain* создайте файл Java с именем *Network.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.domain;
   
   import com.microsoft.spring.data.gremlin.annotation.EdgeSet;
   import com.microsoft.spring.data.gremlin.annotation.Graph;
   import com.microsoft.spring.data.gremlin.annotation.VertexSet;
   import lombok.Getter;
   import org.springframework.data.annotation.Id;
   
   import java.util.ArrayList;
   import java.util.List;
   
   @Graph
   public class Network {
   
       @Id
       private String id;
   
       public Network() {
           this.edges = new ArrayList<Object>();
           this.vertexes = new ArrayList<Object>();
       }
   
       @EdgeSet
       @Getter
       private List<Object> edges;
   
       @VertexSet
       @Getter
       private List<Object> vertexes;
   }
   ```

1. Сохраните и закройте файл *Network.java*.

### <a name="define-a-set-of-classes-that-define-the-repositories-for-your-graph-database"></a>Создание набора классов, которые определяют репозитории графовой базы данных

1. В каталоге пакета своего приложения создайте папку с именем *repository*, например:

   `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\repository`

   -или-

   `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/repository`

1. В каталоге *repository* создайте файл Java с именем *NetworkRepository.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Network;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface NetworkRepository extends GremlinRepository<Network, String> {
   }
   ```

1. Сохраните и закройте файл *NetworkRepository.java*.

1. В каталоге *repository* создайте файл Java с именем *PersonRepository.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Person;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface PersonRepository extends GremlinRepository<Person, String> {
   }
   ```

1. Сохраните и закройте файл *PersonRepository.java*.

1. В каталоге *repository* создайте файл Java с именем *RelationRepository.java*, а затем откройте этот файл в текстовом редакторе и добавьте следующие строки:

   ```java
   package com.example.wingtiptoysdata.repository;
   
   import com.microsoft.spring.data.gremlin.repository.GremlinRepository;
   import com.example.wingtiptoysdata.domain.Relation;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public interface RelationRepository extends GremlinRepository<Relation, String> {
   }
   ```

1. Сохраните и закройте файл *RelationRepository.java*.

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*, например:

   `cd C:\SpringBoot\wingtiptoysdata`

   -или-

   `cd /users/example/home/wingtiptoysdata`

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Ваше приложение отобразит несколько сообщений среды выполнения. При отсутствии ошибок вы сможете просмотреть содержимое базы данных Azure Cosmos DB на портале Azure. Для этого на странице свойств базы данных выберите **Обозреватель данных**, щелкните **Execute Gremlin Query** (Выполнить запрос Gremlin), а затем выберите элемент в списке результатов, чтобы просмотреть данные.

   ![Просмотр данных с помощью обозревателя документов][JV03]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о поддержке Gremlin и API Graph в Azure см. в следующих статьях:

* [Знакомство с Azure Cosmos DB: API Gremlin](/azure/cosmos-db/graph-introduction)

* [Поддержка графа Gremlin в базе данных Azure Cosmos DB](/azure/cosmos-db/gremlin-support)

* [Разработка Создание графовой базы данных с помощью Java и портала Azure](/azure/cosmos-db/create-graph-java)

* [Руководство. Выполнение запросов к API Gremlin в Azure Cosmos DB с использованием Gremlin](/azure/cosmos-db/tutorial-query-graph)

* [Spring Data Gremlin Starter] (Начальное приложение Spring Data Gremlin)

Дополнительные сведения об использовании Azure PowerShell и Java см. в следующих статьях:

* [Документация по Azure Cosmos DB]

* [Разработка Создание ресурсов учетной записи API SQL для Azure Cosmos DB и управление ими с помощью приложения Java][Build a SQL API app with Java]

* [Spring Data для API SQL для Azure Cosmos DB]

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Документация по Azure Cosmos DB]: /azure/cosmos-db/
[Azure для разработчиков Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data для API SQL для Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Data Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin (Начальное приложение Spring Data Gremlin)
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ02.png
[AZ03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ03.png
[AZ05]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ05.png
[AZ06]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ06.png
[AZ07]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ07.png
[AZ08]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/AZ08.png

[SI01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI01.png
[SI03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/SI03.png

[RE01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE01.png
[RE02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/RE02.png

[PM02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/PM02.png

[JV01]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV01.png
[JV02]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV02.png
[JV03]: ./media/configure-spring-data-gremlin-java-app-with-cosmos-db/JV03.png
