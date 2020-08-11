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
ms.custom: devx-track-java
ms.openlocfilehash: 81a80d14e4cf371801cf75af1618048dda8775d7
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810627"
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


## <a name="create-resource"></a>Создание ресурса

### <a name="create-azure-cosmos-db"></a>Создание Azure Cosmos DB

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и щелкните `+Create a resource`.

   >[!div class="mx-imgBorder"]
   >![create-a-resource][create-a-resource-01]

1. Щелкните `Databases`, а затем `Azure Cosmos DB`.

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

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Добавление графа в базу данных Azure Cosmos DB

1. На странице Cosmos DB щелкните `Data Explorer`, а затем `New Graph`.

   >[!div class="mx-imgBorder"]
   >![new-graph][create-a-graph-01]

1. Когда появится `Add Graph`, введите следующие сведения:

   * Укажите уникальный идентификатор `Database id` для базы данных.
   * Можно указать свое значение в поле `Storage capacity` или оставить значение по умолчанию.
   * Укажите уникальный идентификатор `Graph id` для графа.
   * Укажите `Partition key`. Дополнительные сведения см. в статье о [секционировании графов].
Нажмите кнопку `OK`.
   
   Указав эти параметры, щелкните `OK`, чтобы создать граф.

   >[!div class="mx-imgBorder"]
   >![add-graph][create-a-graph-02]

1. После создания графа вы можете просмотреть его с помощью `Data Explorer`.

   >[!div class="mx-imgBorder"]
   >![graph-detail][create-a-graph-03]
   
   

## <a name="create-simple-spring-boot-application-with-the-spring-initializr"></a>Создание простого приложения Spring Boot с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Заполните метаданные проекта, а затем щелкните `GENERATE`:

   >[!div class="mx-imgBorder"]
   >![spring-initializr][spring-initializr-01]

1. Распакуйте файл и импортируйте его в интегрированную среду разработки.


## <a name="update-code-according-to-the-sample-project"></a>Обновление кода на примере проекта

Измените проект, как показано в примере: [azure-spring-data-sample-gremlin].

1. Добавьте зависимость `azure-spring-data-gremlin`

1. Удалите все содержимое `src/test/`

1. Добавьте в `src/main/java` все файлы Java, как это сделано в примере.

1. Обновите конфигурацию в `src/main/resorces/application.properties`, где:

   | Поле              | Описание:                                                                                                                                                                                                             |
   |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `endpoint`         | Указывает URI Gremlin, который создается на основе уникального **ИД**, введенного при создании базы данных Azure Cosmos DB ранее в этом руководстве.                                                 |
   | `port`             | Указывает порт TCP/IP, который для протокола HTTPS должен иметь значение **443**.                                                                                                                                                           |
   | `username`         | Указывает уникальные значения параметров **Идентификатор базы данных** и **Идентификатор графа**, которые использовались при добавлении графа ранее в этом руководстве. Это значение нужно вводить, используя следующий синтаксис: "/dbs/**{Идентификатор базы данных}**/colls/**{Идентификатор графа}**". |
   | `password`         | Указывает основной или дополнительный **ключ доступа**, который вы скопировали ранее в этом руководстве.                                                                                                                      |
   | `sslEnabled`       | Указывает, следует ли разрешить SSL.                                                                                                                                                                                           |
   | `telemetryAllowed` | Укажите **true**, если нужно включить телеметрию. В противном случае укажите **false**.
   | `maxContentLength` | Указывает максимальную длину содержимого.                                                                                                                                                                                           |

1. Как получить пароль:

   >[!div class="mx-imgBorder"]
   >![get-password][get-password-01]

## <a name="build-and-run-the-project"></a>Построение и запуск проекта

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Если приложение успешно запускается, можно проверить граф на портале Azure:

   >[!div class="mx-imgBorder"]
   >![execute-result][execute-result-01]


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

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

* [Развертывание приложения Spring Boot Application в службе приложений Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в Службе контейнеров Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

**[Spring Framework]** — это решение с открытым кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java. В помощь разработчикам, начинающим работать со Spring Boot, по адресу <https://github.com/spring-guides/> доступно несколько примеров пакетов этого приложения. Помимо выбора из списка основных проектов Spring Boot, **[Spring Initializr]** помогает разработчикам создавать пользовательские приложения Spring Boot.

<!-- URL List -->

[Документация по Azure Cosmos DB]: /azure/cosmos-db/
[Azure для разработчиков Java]: /azure/developer/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data для API SQL для Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Data Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin (Начальное приложение Spring Data Gremlin)
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[секционировании графов]: https://docs.microsoft.com/azure/cosmos-db/graph-partitioning
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
