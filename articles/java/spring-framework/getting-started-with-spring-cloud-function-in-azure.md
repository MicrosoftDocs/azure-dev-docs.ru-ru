---
title: Начало работы с функцией Spring Cloud в Azure
description: Узнайте, как использовать функцию Spring Cloud в Azure.
documentationcenter: java
author: jdubois
manager: brborges
ms.author: judubois
ms.date: 07/17/2019
ms.service: azure-functions
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 258b3e8fef4202df3b151ca7058daf72d7be2b61
ms.sourcegitcommit: 2760d3ca0ff0b939181d976a652f2b35ea5b3fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/15/2020
ms.locfileid: "83426090"
---
# <a name="getting-started-with-spring-cloud-function-in-azure"></a>Начало работы с функцией Spring Cloud в Azure

В этой статье описывается, как с помощью [функций Spring Cloud](https://spring.io/projects/spring-cloud-function) разработать функцию Java и опубликовать ее в Функциях Azure. После выполнения действий, описанных в этой статье, код функции будет выполняться в Azure в [Плане потребления](/azure/azure-functions/functions-scale#consumption-plan) и может запускаться с помощью HTTP-запроса.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительные требования

Для разработки функций с помощью Java, должны быть установлены следующие компоненты:

- [Java Developer Kit (JDK)](https://aka.ms/azure-jdks) версии 8.
- [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2) 2.7.1158 или более поздней версии.

> [!IMPORTANT]
> Переменной среде JAVA_HOME необходимо присвоить расположение установки JDK, чтобы завершить выполнение заданий этого краткого руководства.

## <a name="what-we-are-going-to-build"></a>Что мы будем создавать

Мы создадим классическую функцию Hello, World, выполняемую в Функциях Azure и настроенную с помощью функции Spring Cloud.

Функция будет получать простой объект JSON `User`, который содержит имя пользователя, и возвращать объект `Greeting` с приветственным сообщением для этого пользователя.

Проект, который мы создадим в этом примере, доступен здесь: [https://github.com/Azure-Samples/hello-spring-function-azure](https://github.com/Azure-Samples/hello-spring-function-azure). Вы можете использовать этот репозиторий с примером, чтобы просмотреть результат выполнения задачи, которая описана в этом кратком руководстве.

## <a name="create-a-new-maven-project"></a>Создание нового проекта Maven

Мы создадим пустой проект Maven и настроим его с помощью функции Spring Cloud и Функций Azure.

В пустой папке создайте файл *pom.xml*, а затем скопируйте и вставьте содержимое из нашего примера проекта, доступного здесь: [https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml](https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml).

> [!NOTE]
> В этом файле используются зависимости Maven от Spring Boot и функции Spring Cloud, а также настраиваются плагины Maven для Spring Boot и Функций Azure.

Для приложения необходимо настроить несколько свойств:

- `<functionAppName>` — это имя функции Azure.
- `<functionAppRegion>` — это имя региона Azure, в котором развернута функция.
- `<functionResourceGroup>` — имя используемой группы ресурсов Azure.

Эти свойства следует изменить прямо в верхней части файла *pom.xml*:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <azure.functions.maven.plugin.version>1.4.1</azure.functions.maven.plugin.version>
    <azure.functions.java.library.version>1.3.0</azure.functions.java.library.version>
    <functionAppName>my-spring-function</functionAppName>
    <functionAppRegion>westus</functionAppRegion>
    <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    <functionResourceGroup>my-resource-group</functionResourceGroup>
    <start-class>com.example.HelloFunction</start-class>
    <spring.boot.wrapper.version>1.0.24.RELEASE</spring.boot.wrapper.version>
</properties>
```

## <a name="create-azure-configuration-files"></a>Создание файлов конфигурации в Azure

Создайте папку *src/main/azure* и добавьте в нее следующие файлы конфигурации Функций Azure.

*host.json*:

```json
{
  "version": "2.0",
  "functionTimeout": "00:10:00"
}
```

*local.settings.json*:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "java",
    "AzureWebJobsDashboard": ""
  }
}
```

## <a name="create-domain-objects"></a>Создание объектов домена

Функции Azure могут получать и отправлять объекты в формате JSON.
Сейчас мы создадим объекты `User` и `Greeting`, которые представляют модель предметной области.
Вы можете создать более сложные объекты с дополнительными свойствами, если хотите настроить пример из этого руководства и сделать его более интересным.

Создайте папку *src/main/java/com/example/model* и добавьте в нее следующие два файла.

*User. Java*:

```java
package com.example.model;

public class User {

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

*Greeting.java*:

```java
package com.example.model;

public class Greeting {

    public Greeting() {
    }

    public Greeting(String message) {
        this.message = message;
    }

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

## <a name="create-the-spring-boot-application"></a>Создание приложения Spring Boot

Это приложение будет управлять всей бизнес-логикой, а также будет иметь доступ к полной экосистеме Spring Boot.
Таким образом, вы получаете два основных преимущества по сравнению со стандартной Функцией Azure:

- Отсутствует зависимость от API Функций Azure, что позволяет легко перенести функцию в другие системы. Например, функцию можно использовать повторно в обычном приложении Spring Boot.
- Можно использовать все аннотации `@Enable` от Spring Boot, чтобы легко добавлять новые мощные функции.

В папке *src/main/java/com/example* создайте следующий файл, который представляет собой обычное приложение Spring Boot.

*HelloFunction.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.util.function.Function;

@SpringBootApplication
public class HelloFunction {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(HelloFunction.class, args);
    }

    @Bean
    public Function<User, Greeting> hello() {
        return user -> new Greeting("Welcome, " + user.getName());
    }
}
```

> [!NOTE] 
> Функция `hello()` довольно специфична:
> 
> - Она возвращает объект `java.util.function.Function`, представляющий собой функцию, которая будет использоваться при работе с этим кратким руководством. Она содержит бизнес-логику и использует стандартный API Java для преобразования одного объекта в другой.
> - Так как у функции есть аннотация `@Bean`, она представляет собой сущность Spring Bean, и по умолчанию ее имя будет указывать на один из методов (`hello`). Это необходимое условие при создании в приложении других функций, потому что это имя должно соответствовать имени функции Azure, создание которой описано в следующем разделе.

## <a name="create-the-azure-function"></a>Создание функции Azure

Чтобы воспользоваться всеми преимуществами полноценного API Функций Azure, мы создадим определенный класс. Он будет представлять собой функцию Azure, которая делегирует выполнение функции Spring Cloud, созданной на предыдущем шаге.

В папке *src/main/java/com/example* создайте следующую Функцию Azure.

*HelloHandler.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.AuthorizationLevel;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.HttpTrigger;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import java.util.Optional;

public class HelloHandler extends AzureSpringBootRequestHandler<User, Greeting> {

    @FunctionName("hello")
    public HttpResponseMessage execute(
            @HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<User>> request,
            ExecutionContext context) {

        context.getLogger().info("Greeting user name: " + request.getBody().get().getName());
        return request
                .createResponseBuilder(HttpStatus.OK)
                .body(handleRequest(request.getBody().get(), context))
                .header("Content-Type", "application/json")
                .build();
    }
}
```

Этот класс Java представляет собой Функцию Azure со следующими интересными возможностями:

- Функция расширяет возможности объекта `AzureSpringBootRequestHandler`, который создает связь между Функциями Azure и функцией Spring Cloud. Таким образом предоставляется метод `handleRequest()`, который используется в методе `execute()`.
- Имя функции, как определено в аннотации `@FunctionName("hello")`, совпадает с именем сущности Spring Bean (`hello`), которую мы настроили на предыдущем шаге.
- Это реальная функция Azure, поэтому вы можете использовать полный API Функций Azure в этом примере.

## <a name="add-unit-tests"></a>Добавление модульных тестов

Конечно, этот шаг является необязательным, но как хороший разработчик вы должны добавить модульные тесты, чтобы проверить, правильно ли работает приложение.

Создайте папку *src/test/java/com/example* и добавьте в нее следующие модульные тесты JUnit.

*HelloFunctionTest.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.junit.Test;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloFunctionTest {

    @Test
    public void test() {
        Greeting result = new HelloFunction().hello().apply(new User("foo"));
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }

    @Test
    public void start() throws Exception {
        AzureSpringBootRequestHandler<User, Greeting> handler = new AzureSpringBootRequestHandler<>(
                HelloFunction.class);
        Greeting result = handler.handleRequest(new User("foo"), null);
        handler.close();
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }
}
```

Теперь можно протестировать функцию Azure с помощью Maven:

```bash
mvn clean test
```

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Прежде чем развертывать приложение в Функции Azure, протестируйте его локально.

Сначала упакуйте приложение в JAR-файл:

```bash
mvn package
```

Теперь, когда приложение упаковано, вы можете запустить его с помощью подключаемого модуля Maven `azure-functions`:

```bash
mvn azure-functions:run
```

Функция Azure должна быть доступна на локальном узле, использующем порт 7071. Чтобы протестировать функцию, можете отправить ей запрос POST с объектом `User` в формате JSON. Например, это можно сделать с помощью cURL:

```bash
curl http://localhost:7071/api/hello -d "{\"name\":\"Azure\"}"
```

Функция должна отправить ответ с объектом `Greeting` в том же формате JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Ниже приведен снимок экрана, в верхней части которого показан запрос cURL, а в нижней — локальная Функция Azure.

 ![Функция Azure, выполняемая локально][RFL01]

## <a name="deploy-the-function-to-azure-functions"></a>Развертывание Функции в Функциях Azure

На этом этапе вы опубликуете Функцию Azure в рабочей среде. Помните, что для настройки функции будут использоваться свойства `<functionAppName>`, `<functionAppRegion>` и `<functionResourceGroup>`, которые определенны в файле *pom.xml*.

> [!NOTE]
> Подключаемый модуль Maven должен пройти проверку подлинности в Azure. Если у вас установлен Azure CLI, выполните команду `az login`, прежде чем продолжить.
> Сведения о других вариантах проверки подлинности см. [здесь](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Запустите Maven, чтобы автоматически развернуть функцию:

```bash
mvn azure-functions:deploy
```

Теперь перейдите на [портал Azure](https://portal.azure.com), чтобы найти созданный объект `Function App`.

Щелкните имя функции:

- В общих сведениях о функции обратите внимание на ее URL-адрес.
- Перейдите на вкладку **Функции платформы**, чтобы найти службу **Потоковая передача журналов**, а затем выберите эту службу для проверки выполняемой функции.

Теперь, как и в предыдущем разделе, получите доступ к выполняемой функции, используя cURL. Замените `your-function-name` именем реальной функции:

```bash
curl https:/your-function-name.azurewebsites.net/api/hello -d "{\"name\":\"Azure\"}"
```

Как и в предыдущем разделе, Функция должна отправить ответ с объектом `Greeting` в том же формате JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Поздравляем! У вас есть функция Spring Cloud, выполняемая в Функциях Azure.

<!-- IMG List -->

[RFL01]: media/getting-started-with-spring-cloud-function-in-azure/RFL01.png
