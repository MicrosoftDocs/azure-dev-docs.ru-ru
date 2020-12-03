---
title: Использование начального приложения Spring Data Gremlin с API SQL Azure Cosmos DB
description: Сведения о настройке приложения, созданного с помощью Spring Boot Initializr в API SQL для Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 10/14/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 9a04f3eac457d22c728a11850d4a85aba88a71df
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442005"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Использование начального приложения Spring Data Gremlin с API SQL Azure Cosmos DB

В этой статье показано, как с помощью портала Azure создать Azure Cosmos DB ресурса для работы с API Gremlin. Также здесь показано, как использовать [Spring Initializr] для создания пользовательского приложения Java, а затем добавить возможности Spring Data Gremlin Starter для доступа к данным с помощью Gremlin.

Начальное приложение Spring Data Gremlin обеспечивает поддержку Spring Data для языка запросов Gremlin от Apache, который разработчики могут использовать с любым хранилищем данных, совместимым с Gremlin.

## <a name="prerequisites"></a>предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие компоненты:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.


## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Создание учетной записи Cosmos DB с помощью портала Azure

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выберите `+Create a resource`.

   >[!div class="mx-imgBorder"]
   >![create-a-resource][create-a-resource-01]

1. Выберите `Databases`, а затем `Azure Cosmos DB`.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db][create-a-resource-02]

1. На странице `Azure Cosmos DB` введите следующие сведения:

   * Выберите `Subscription` для использования с базой данных.
   * Укажите, следует ли создать `Resource Group` для базы данных, или выберите имеющуюся группу ресурсов.
   * Введите уникальное имя `Account Name`, которое будет использоваться как элемент URI Gremlin для вашей базы данных. Например, если вы указали `account-sample` для `Account Name`, URI Gremlin будет `account-samplewingtiptoysdata.gremlin.cosmosdb.azure.com`.
   * Выберите `Gremlin (Graph)` для API.
   * Укажите `Location` для базы данных.
   
1. Указав эти параметры, щелкните `Review + create`.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db-account][create-a-resource-03]

1. Проверьте спецификацию и щелкните `Create`, чтобы создать базу данных.

1. Когда завершится создание базы данных, щелкните **Перейти к ресурсу**. База данных отобразится на **панели мониторинга** Azure, а также на страницах **Все ресурсы** и **Azure Cosmos DB**. Вы можете выбрать базу данных в любом из этих расположений, чтобы открыть страницу свойств кэша.

1. Когда откроется страница свойств базы данных, щелкните **Ключи** и скопируйте URI и ключи доступа для базы данных. Эти значения будут использоваться в приложении Spring Boot.

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Добавление графа в базу данных Azure Cosmos DB

1. На странице Cosmos DB щелкните `Data Explorer`, а затем `New Graph`.

   >[!div class="mx-imgBorder"]
   >![new-graph][create-a-graph-01]

1. Когда появится `Add Graph`, введите следующие сведения:

   * Укажите уникальный идентификатор `Database id` для базы данных.
   * Можно указать свое значение в поле `Storage capacity` или оставить значение по умолчанию.
   * Укажите уникальный идентификатор `Graph id` для графа.
   * Укажите `Partition key`. Дополнительные сведения см. в статье о [секционировании графов]. Выберите `OK`.
   
   Указав эти параметры, щелкните `OK`, чтобы создать граф.

   >[!div class="mx-imgBorder"]
   >![add-graph][create-a-graph-02]

1. После создания графа вы можете просмотреть его с помощью `Data Explorer`.

   >[!div class="mx-imgBorder"]
   >![graph-detail][create-a-graph-03]
   
   

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что требуется создать проект **Maven** на **Java**, введите имя **группы** и **артефакта** для приложения, укажите для версии **Spring Boot** значение 2.3.4, а затем щелкните **GENERATE** (Создать).

   >[!div class="mx-imgBorder"]
   >![spring-initializr][spring-initializr-01]
   
   > [!NOTE]
   > Spring Initializr использует имена **группы** и **артефакта**, чтобы создать имя пакета, например `com.example.wintiptoysdata`.

1. При появлении запроса скачайте проект на локальный компьютер.

1. После извлечения файлов в локальной системе импортируйте их в интегрированную среду разработки.


## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Настройка приложения Spring Boot для использования начального приложения Spring Data Gremlin

Мы реплицируем конфигурации существующего [примера Gremlin для Azure Spring Data](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin). Перейдите к этому примеру и выполните описанные в этом разделе действия, чтобы настроить приложение Spring Boot.

1. Найдите файл *pom.xml* в каталоге приложения, например:

   *C:\SpringBoot\wingtiptoysdata\pom.xml*

   -или-

   */users/example/home/wingtiptoysdata/pom.xml*

1. Откройте файл *pom.xml* и добавьте Spring Data Gremlin Starter в список `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-gremlin</artifactId>
      <version>2.3.1-beta.1</version> <!-- {x-version-update;com.azure:azure-spring-data-gremlin;current} -->
    </dependency>
   ```

1. Сохраните и закройте файл *pom.xml*.

1. Перейдите в папку *src/test/* и удалите все ее содержимое.

1. Перейдите в папку *src/main/java* в примере приложения, а затем скопируйте и перезапишите этот же каталог в локальное приложение Spring Boot.

1. В файле *src/main/resources/application.properties* измените конфигурации, включив в них следующее:

   | Поле              | Описание                                                                                                                                                                                                             |
   |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `endpoint`         | Указывает URI Gremlin для вашей базы данных, который создается на основе уникального **ИД**, введенного при создании базы данных Azure Cosmos DB ранее в этом кратком руководстве.                                                 |
   | `port`             | Указывает порт TCP/IP, который для протокола HTTPS должен иметь значение **443**.                                                                                                                                                           |
   | `username`         | Указывает уникальные значения параметров **Идентификатор базы данных** и **Идентификатор графа**, которые использовались при добавлении графа ранее в этом руководстве. Это значение нужно вводить, используя следующий синтаксис: "/dbs/ **{идентификатор базы данных}** /colls/ **{идентификатор графа}** ". |
   | `password`         | Указывает основной или дополнительный **ключ доступа**, который вы скопировали ранее в этом кратком руководстве.                                                                                                                      |
   | `sslEnabled`       | Указывает, следует ли разрешить SSL.                                                                                                                                                                                           |
   | `telemetryAllowed` | Укажите **true**, если нужно включить телеметрию. В противном случае укажите **false**.
   | `maxContentLength` | Указывает максимальную длину содержимого.                                                                                                                                                                                           |

## <a name="build-and-run-the-project"></a>Построение и запуск проекта

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Если приложение успешно запускается, можно проверить граф на портале Azure:

   >[!div class="mx-imgBorder"]
   >![execute-result][execute-result-01]


## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Spring в Azure см. в соответствующей документации.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о поддержке Gremlin и API Graph в Azure см. в следующих статьях:

* [Знакомство с базой данных Azure Cosmos DB. API Graph](/azure/cosmos-db/graph-introduction)

* [Поддержка графа Gremlin в базе данных Azure Cosmos DB](/azure/cosmos-db/gremlin-support)

* [Azure Cosmos DB: создание графовой базы данных с помощью Java и портала Azure](/azure/cosmos-db/create-graph-java)

* [Руководство по выполнению запросов к API Graph в Azure Cosmos DB с использованием Gremlin](/azure/cosmos-db/tutorial-query-graph)

* [Spring Data Gremlin Starter] (Начальное приложение Spring Data Gremlin)

Дополнительные сведения об использовании Azure PowerShell и Java см. в следующих статьях:

* [Документация по Azure Cosmos DB]

* [Разработка Создание ресурсов учетной записи API SQL для Azure Cosmos DB и управление ими с помощью приложения Java][Build a SQL API app with Java]

* [Spring Data для API SQL для Azure Cosmos DB]

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot в Службе приложений Azure в Linux](deploy-spring-boot-java-app-on-linux.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Документация по Azure Cosmos DB]: /azure/cosmos-db/
[Azure для разработчиков Java]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data для API SQL для Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Data Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin (Начальное приложение Spring Data Gremlin)
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[секционировании графов]: /azure/cosmos-db/graph-partitioning
[azure-spring-data-sample-gremlin]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin

<!-- IMG List -->

[create-a-resource-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-01.png
[create-a-resource-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-02.png
[create-a-resource-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-03.png

[create-a-graph-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-01.png
[create-a-graph-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-02.png
[create-a-graph-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-03.png

[spring-initializr-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/spring-initializr-01.png

[get-password-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/get-password-01.png

[execute-result-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/execute-result-01.png
