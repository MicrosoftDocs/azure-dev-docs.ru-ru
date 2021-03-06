---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: 012043df3cf07de098d1a7f3a6715374814d1d9b
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507723"
---
Создайте новый класс Java `Todo` рядом с классом `DemoApplication` и добавьте следующий код:

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

import org.springframework.data.repository.CrudRepository;

public interface TodoRepository extends CrudRepository<Todo, Long> {
}
```

Этим репозиторием управляет Spring Data JDBC.

Завершите работу приложения, создав контроллер, который может хранить и извлекать данные. Реализуйте класс `TodoController` в том же пакете и добавьте следующий код:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Todo createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Iterable<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Остановите приложение и запустите его снова с помощью следующей команды:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Тестирование приложения

Чтобы протестировать приложение, можно использовать cURL.

Сначала создайте новый элемент todo в базе данных с помощью следующей команды:

```bash
curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"configuration","details":"congratulations, you have set up JDBC correctly!","done": "true"}' \
    http://127.0.0.1:8080
```

Эта команда должна возвращать созданный элемент:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up JDBC correctly!","done":true}
```

Затем извлеките данные, используя новый запрос cURL:

```bash
curl http://127.0.0.1:8080
```

Эта команда вернет список элементов todo, включая созданный вами элемент:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up JDBC correctly!","done":true}]
```
