---
title: Как использовать API Apache Cassandra Spring Data с Azure Cosmos DB
description: Узнайте, как использовать API Apache Cassandra Spring Data с Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 10/13/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 7879a47bdcbc9b1a4cf41210fc9fb49ad28d8dd8
ms.sourcegitcommit: 8e1d3a384ccb0e083589418d65a70b3a01afebff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560345"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Как использовать API Apache Cassandra Spring Data с Azure Cosmos DB

В этой статье показано создание примера приложения, использующего [Spring Data] для хранения и извлечения информации с помощью [API Cassandra для Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если у вас ее еще нет, вы можете активировать [Преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.
* [Curl](https://curl.haxx.se/) или подобная служебная HTTP-программа, с помощью которой можно протестировать функциональные возможности.
* Клиент [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Следующая процедура создает и настраивает учетную запись Cosmos на портале Azure.

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Создание учетной записи Cosmos DB с помощью портала Azure

> [!NOTE]
> 
> Более подробные сведения о создании учетных записей Azure Cosmos DB см. в статье [о документации по Azure Cosmos DB](/azure/cosmos-db/).

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Выберите **Создать ресурс**, **Начало работы** и **Azure Cosmos DB**.
    
   >[!div class="mx-imgBorder"]
   >![Создание учетной записи Azure Cosmos DB][COSMOSDB01]

1. Укажите следующие сведения:

   - **Подписка**: Укажите подписку Azure, которую нужно использовать.
   - **Группа ресурсов.** Укажите, следует ли создать группу ресурсов, или выберите имеющуюся группу ресурсов.
   - **Имя учетной записи**. Для учетной записи Cosmos DB выберите уникальное имя. Это имя будет использоваться для создания полного доменного имени, например *wingtiptoyscassandra.documents.azure.com*.
   - **API**. Для работы с этим руководством выберите *Cassandra*.
   - **Расположение.** Укажите ближайший географический регион для базы данных.
   
   >[!div class="mx-imgBorder"]
   >![Указание параметров учетной записи Cosmos DB][COSMOSDB02]
   
1. После ввода всех этих данных нажмите кнопку **Отзыв и создание**.

1. Если при просмотре страницы все выглядит правильно, щелкните **Создать**.
   
   >[!div class="mx-imgBorder"]
   >![Проверка параметров учетной записи Cosmos DB][COSMOSDB03]

Развертывание базы данных занимает несколько минут.

### <a name="add-a-keyspace-to-your-azure-cosmos-db-account"></a>Добавление пространства ключей к учетной записи Azure Cosmos DB

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Выберите **Все ресурсы**, а затем щелкните только что созданную учетную запись Azure Cosmos DB.

1. Выберите **Обозреватель данных**, щелкните стрелку вниз и выберите пункт **New Keyspace** (Создать пространство ключей). Введите уникальный идентификатор в поле **Keyspace id** (Идентификатор пространства ключей), а затем нажмите кнопку **ОК**.
    
   >[!div class="mx-imgBorder"]
   >![Выбор элемента для создания пространства ключей][COSMOSDB05]
   
   >[!div class="mx-imgBorder"]
   >![Создание пространства ключей Cosmos DB][COSMOSDB05-1]

### <a name="retrieve-the-connection-settings-for-your-azure-cosmos-db-account"></a>Получение параметров подключения к учетной записи Azure Cosmos DB

1. Перейдите на портал Azure по адресу <https://portal.azure.com/> и выполните вход.

1. Выберите **Все ресурсы**, а затем щелкните только что созданную учетную запись Azure Cosmos DB.

1. Выберите **Строки подключения** и скопируйте соответствующие значения для полей **Точка контакта**, **Порт**, **Имя пользователя** и **Основной пароль**. Позже эти значения будут использоваться для настройки приложения.
   
   >[!div class="mx-imgBorder"]
   >![Получение параметров подключения к Cosmos DB][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Настройка примера приложения

Следующая процедура настраивает тестовое приложение.

1. Откройте командную строку и клонируйте пример проекта с помощью команды Git, как в следующем примере:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure.git
   ```

1. Найдите файл *application.properties* в каталоге *resources* примера приложения или создайте его, если он еще не существует.

1. Откройте файл *application.properties* в текстовом редакторе, добавьте или настройте указанные ниже строки в файл и замените примеры значений на соответствующие полученные ранее значения:

   ```yaml
   spring.data.cassandra.contact-points=wingtiptoyscassandra.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=wingtiptoyscassandra
   spring.data.cassandra.password=********
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `spring.data.cassandra.contact-points` | Указывается **точка контакта**, описанная ранее в этой статье. |
   | `spring.data.cassandra.port` | Указывается **порт**, описанный ранее в этой статье. |
   | `spring.data.cassandra.username` | Указывается **имя пользователя**, описанное ранее в этой статье. |
   | `spring.data.cassandra.password` | Указывается **основной пароль**, описанный ранее в этой статье. |

1. Сохраните и закройте файл *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Упаковывание и тестирование примера приложения 

Перейдите в каталог, содержащий файл .pom, чтобы создать и протестировать приложение.

1. Создайте пример приложения с помощью Maven, например:

   ```shell
   mvn clean package
   ```

1. Запустите пример приложения, например:

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Из командной строки создайте записи с помощью `curl`, как в следующем примере:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Приложение должно возвращать значения следующим образом:

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Получите все имеющиеся записи из командной строки с помощью `curl`, как в следующем примере:

   ```shell
   curl -s http://localhost:8080/pets
   ```

   Приложение должно возвращать значения следующим образом:

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Итоги

С помощью этого руководства вы создали пример приложения Java, использующий Spring Data для хранения и извлечения информации с помощью API Cassandra для Azure Cosmos DB.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

<!-- URL List -->

[Azure для разработчиков Java]: ../index.yml
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB05]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-05.png
[COSMOSDB05-1]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-05-1.png
[COSMOSDB06]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-06.png