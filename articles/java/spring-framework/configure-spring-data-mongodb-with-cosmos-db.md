---
title: Как использовать API MongoDB Spring Data с Azure Cosmos DB
description: Узнайте, как использовать API MongoDB Spring Data с Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 12/19/2018
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 706797811e699261bd8eebe82e9b31d544fc703b
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673220"
---
# <a name="how-to-use-spring-data-mongodb-api-with-azure-cosmos-db"></a>Как использовать API MongoDB Spring Data с Azure Cosmos DB

## <a name="overview"></a>Обзор

В этой статье показано создание примера приложения, использующего [Spring Data] для хранения и извлечения информации с помощью [API MongoDB для Azure Cosmos DB](/azure/cosmos-db/mongodb-introduction).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* Клиент [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Создание учетной записи Cosmos DB с помощью портала Azure

> [!NOTE]
> 
> Более подробные сведения о создании учетных записей Azure Cosmos DB см. в статье [о документации по Azure Cosmos DB](/azure/cosmos-db/).

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Щелкните элемент **+Create a resource** (+Создать ресурс), **Базы данных**, а затем выберите **Azure Cosmos DB**.

   ![создание учетной записи Azure Cosmos DB;][COSMOSDB01]

1. Укажите следующие сведения:

   - **Подписка**: Укажите подписку Azure, которую нужно использовать.
   - **Группа ресурсов.** Укажите, следует ли создать группу ресурсов, или выберите имеющуюся группу ресурсов.
   - **Имя учетной записи**. Для учетной записи Cosmos DB выберите уникальное имя. Это имя будет использоваться для создания полного доменного имени, например *wingtiptoysmongodb.documents.azure.com*.
   - **API**. В данном руководстве укажите `Azure Cosmos DB for MongoDB API`.
   - **Расположение.** Укажите ближайший географический регион для базы данных.

   ![Указание параметров учетной записи Cosmos DB][COSMOSDB02]
   
1. После ввода всех этих данных нажмите кнопку **Отзыв и создание**.

1. Если при просмотре страницы все выглядит правильно, щелкните **Создать**.

   ![Проверка параметров учетной записи Cosmos DB][COSMOSDB03]

### <a name="retrieve-the-connection-string-for-your-azure-cosmos-db-account"></a>Получение строки подключения к учетной записи Azure Cosmos DB

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Нажмите кнопку **Все ресурсы**, а затем щелкните только что созданную учетную запись Azure Cosmos DB.

1. Нажмите кнопку **Строки подключения** и скопируйте значение для поля **Основная строка подключения**. Позже это значение будет использоваться для настройки приложения.

   ![Получение строк подключения к Cosmos DB][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте командную строку и клонируйте пример проекта с помощью команды Git, как в следующем примере:

   ```shell
   git clone https://github.com/spring-guides/gs-accessing-data-mongodb.git
   ```

1. Создайте каталог *resources* в каталоге примера проекта *&lt;project root&gt;/complete/src/main*, а затем создайте файл *application.properties* в каталоге *resources*.

1. Откройте файл *application.properties* в текстовом редакторе и добавьте указанные ниже строки в файл и замените примеры значений на соответствующие полученные ранее значения:

   ```yaml
   spring.data.mongodb.database=wingtiptoysmongodb
   spring.data.mongodb.uri=mongodb://wingtiptoysmongodb:AbCdEfGhIjKlMnOpQrStUvWxYz==@wingtiptoysmongodb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `spring.data.mongodb.database` | Определяет имя учетной записи Cosmos DB, описанное в этой статье. |
   | `spring.data.mongodb.uri` | Указывает **основную строку подключения**, описанную в этой статье. |

1. Сохраните и закройте файл *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Упаковывание и тестирование примера приложения

Чтобы создать приложение, перейдите в каталог */gs-accessing-data-mongodb/complete*, в котором содержится файл pom.xml.

1. Создайте пример приложения с помощью Maven, а затем настройте Maven на пропуск тестов, например:

   ```shell
   mvn clean package -DskipTests
   ```

1. Запустите пример приложения, например:

   ```shell
   java -jar target/accessing-data-mongodb-0.0.1-SNAPSHOT.jar
   ```
    
   Приложение должно возвращать значения следующим образом:

   ```json
   Customers found with findAll():
   -------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   
   Customer found with findByFirstName('Alice'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customers found with findByLastName('Smith'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   ```

## <a name="summary"></a>Сводка

С помощью этого руководства вы создали пример приложения Java, использующий Spring Data для хранения и извлечения информации с помощью API MongoDB для Azure Cosmos DB.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Azure для разработчиков Java]: /azure/developer/java/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB04]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-04.png
[COSMOSDB06]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-06.png
