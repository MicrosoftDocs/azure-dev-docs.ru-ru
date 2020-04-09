---
title: Сведения об использовании R2DBC Spring Data с Базой данных Azure для MySQL
description: Сведения об использовании R2DBC Spring Data с Базой данных Azure для MySQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: article
ms.author: judubois
ms.openlocfilehash: 32008e4ddbf2663050dd1241e1d028e66ffb7dab
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80611909"
---
# <a name="how-to-use-spring-data-r2dbc-with-azure-mysql"></a>Как использовать R2DBC Spring Data с базой данных Azure MySQL

В этом разделе показано, как создать пример приложения для хранения информации в [Базе данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/) и ее извлечения с помощью [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc), используя реализацию R2DBC для MySQL [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

[R2DBC](https://r2dbc.io/) приносит реактивные API к традиционным реляционным базам данных. Его можно использовать с Spring WebFlux для создания полностью реактивных приложений Spring Boot с использованием неблокирующих API и обеспечения лучшей масштабируемости по сравнению с классическим подходом "один поток на подключение".

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure. Если у вас ее нет, [получите бесплатную пробную версию](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) или [Azure CLI](/cli/azure/install-azure-cli) Мы рекомендуем использовать Azure Cloud Shell, так вы автоматически войдете в систему и получите доступ ко всем необходимым средствам.
- [Java 8](https://www.azul.com/downloads/zulu/) (в составе Azure Cloud Shell)
- [cURL](https://curl.haxx.se) или подобная служебная HTTP-программа, с помощью которой можно протестировать функциональные возможности.

## <a name="prepare-the-working-environment"></a>Подготовка среды выполнения

Сначала настройте некоторые переменные среды с помощью следующих команд:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=r2dbc
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Замените заполнители следующими значениями, которые используются в этом разделе:

- `<YOUR_DATABASE_NAME>`: имя экземпляра сервера MySQL. Оно должно быть уникальным в Azure.
- `<YOUR_AZURE_REGION>`: регион Azure, который вы будете использовать. Вы можете использовать `eastus` по умолчанию, но рекомендуется настроить регион, расположенный ближе к месту проживания. Полный список доступных регионов можно получить, введя `az account list-locations`.
- `<YOUR_MYSQL_PASSWORD>`: пароль вашего сервера базы данных MySQL. Этот пароль должен содержать не менее восьми символов из таких трех категорий: латинские прописные и строчные буквы, цифры (0–9) и другие символы (!, $, #, % и т. д.).
- `<YOUR_LOCAL_IP_ADDRESS>`: IP-адрес локального компьютера, с которого будет запускаться приложение Spring Boot. Одним из удобных способов его поиска является указание обозревателю [http://whatismyip.akamai.com/](http://whatismyip.akamai.com/).

Затем создайте группу ресурсов.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Чтобы отобразить данные JSON и сделать их более удобными для чтения, мы используем служебную программу `jq`, которая по умолчанию устанавливается в [Azure Cloud Shell](https://shell.azure.com/).
> Если вам не нравится эта служебная программа, вы можете безопасно удалить `| jq` часть всех команд, которые мы будем использовать.

## <a name="create-an-azure-database-for-mysql"></a>Создание Базы данных Azure для MySQL

Первое, что мы создадим, — это управляемый экземпляр Сервера MySQL.

> [!NOTE]
> 
> Дополнительные сведения см. в статье о [создании базы данных Azure для сервера MySQL с помощью портала Azure](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

По-прежнему в экземпляре [Azure Shell](https://shell.azure.com/) выполните следующий скрипт:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    | jq
```

Эта команда создаст небольшой экземпляр сервера MySQL.

### <a name="configure-a-firewall-rule-for-your-mysql-server-instance"></a>Настройка правила брандмауэра для экземпляра сервера MySQL

Экземпляры Базы данных Azure для MySQL защищены по умолчанию: они имеют брандмауэр, который не разрешает какое-либо входящее подключение. Чтобы иметь возможность использовать нашу базу данных, необходимо добавить правило брандмауэра, которое позволит локальному IP-адресу получить доступ к серверу базы данных.

Так как мы настроили локальный IP-адрес в начале этой статьи, вы можете открыть брандмауэр сервера, выполнив следующую команду:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-mysql-database"></a>Настройка базы данных MySQL

Сервер MySQL, который мы создали ранее, пуст: он не имеет базы данных, которую мы можем использовать с приложением Spring Boot. Создайте новую базу данных с именем `r2dbc`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name r2dbc \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

## <a name="create-a-reactive-spring-boot-application"></a>Создание реактивного приложения Spring Boot

Чтобы создать реактивное приложение Spring Boot, мы будем использовать [Spring Initializr](https://start.spring.io/). Приложение, которое мы создадим, использует:

- Spring Boot 2.3.0 M3
- Java 8 (но он также будет работать с более новыми версиями, такими как Java 11)
- Следующие зависимости: Spring Reactive Web (также известный как "Spring WebFlux") и Spring Data R2DBC.

### <a name="generate-the-application-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

Создайте это приложение с помощью командной строки, введя:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-r2dbc-workshop -d bootVersion=2.3.0.M3 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Добавление реализации реактивного драйвера MySQL

Откройте файл *pom.xml* созданного проекта, чтобы добавить реактивный драйвер MySQL из [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

После зависимости `spring-boot-starter-webflux` добавьте следующий фрагмент кода:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-the-azure-database-for-mysql"></a>Настройка Spring Boot на использование базы данных Azure для MySQL

Откройте файл *src/main/resources/application.properties* и добавьте:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/r2dbc
spring.r2dbc.username=r2dbc@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_USERNAME
```

- Замените две переменные `$AZ_DATABASE_NAME` значением, которое вы настроили в начале этой статьи.
- Замените переменную `$AZ_MYSQL_USERNAME` значением, которое вы настроили в начале этой статьи.

Теперь вы можете запустить приложение с помощью предоставленной оболочки Maven:

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана приложения, выполняемого в первый раз:

![Выполнение приложения][R2DBC-MYSQL01]

### <a name="create-the-database-schema"></a>Создание схемы базы данных

В основном классе `DemoApplication` настройте новый bean-компонент Spring, который создаст схему базы данных, которую вы будете использовать:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Этот bean-компонент Spring использует файл с именем *schema.sql*, поэтому создайте этот файл в папке *src/main/resources*.

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Используйте следующую команду, чтобы закрыть приложение и запустить его снова. Теперь приложение будет использовать созданную ранее базу данных `r2dbc` и создаст в ней таблицу `todo`.

```bash
./mvnw spring-boot:run
```

Ниже приведен снимок экрана создаваемой таблицы базы данных:

   ![Создание таблицы базы данных][R2DBC-MYSQL02]

## <a name="code-the-application"></a>Добавление кода приложения

Затем добавьте код Java, который будет использовать R2DBC для хранения и извлечения данных из экземпляра сервера MySQL.

Теперь создайте новый класс Java `Todo` рядом с классом `DemoApplication`:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Этот класс является моделью предметной области, сопоставленной с таблицей `todo`, созданной ранее.

Для управления этим классом потребуется репозиторий. Определите новый интерфейс `TodoRepository` в том же пакете:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

Этот репозиторий является реактивным репозиторием, управляемым Spring Data R2DBC.

Затем завершите работу приложения, создав контроллер, который может хранить и извлекать данные. Реализуйте класс `TodoController` в том же пакете и добавьте следующий код:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Наконец, остановите приложение и запустите его снова:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Тестирование приложения

Чтобы протестировать приложение, можно использовать cURL.

Сначала создайте новый элемент "todo" в базе данных:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

Эта команда должна возвращать созданный элемент:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Далее, извлеките данные, используя новый запрос cURL:

```bash
curl http://127.0.0.1:8080
```

Эта команда вернет список "todos", включая созданный вами элемент:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```

Ниже приведен снимок экрана с этими запросами:

   ![Тестирование с помощью cURL][R2DBC-MYSQL03]

Поздравляем! Вы создали полностью реактивное приложение Spring Boot, которое использует R2DBC для хранения и извлечения данных из Базы данных Azure для MySQL.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить все ресурсы, используемые во время этого краткого руководства, удалите группу ресурсов:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения о Spring Data R2DBC см. [Справочную документацию](https://docs.spring.io/spring-data/r2dbc/docs/1.0.x/reference/html/#reference) по Spring.

Дополнительные сведения об использовании Java в Azure см. на страницах [Azure для разработчиков Java](/azure/java/) и [Работа с Azure DevOps и Java](/azure/devops/).

<!-- IMG List -->

[R2DBC-MYSQL01]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png
[R2DBC-MYSQL02]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png
[R2DBC-MYSQL03]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png
