---
author: judubois
ms.date: 06/16/2020
ms.author: judubois
ms.openlocfilehash: 4186673ede494fc16012416d354d821df08e8810
ms.sourcegitcommit: 3b069f1f89492f7e7bc5952a14dbfdde71d1e576
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85107654"
---
Создайте новый класс Java `Todo` рядом с классом `DemoApplication` и добавьте следующий код:

```java
package com.example.demo;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    @GeneratedValue
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

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (!(o instanceof Todo)) {
            return false;
        }
        return id != null && id.equals(((Todo) o).id);
    }

    @Override
    public int hashCode() {
        return 31;
    }
}
```

Этот класс является моделью предметной области, сопоставленной с таблицей `todo`, которая будет автоматически создана JPA.

Для управления этим классом потребуется репозиторий. Определите новый интерфейс `TodoRepository` в том же пакете:

```java
package com.example.demo;

import org.springframework.data.jpa.repository.JpaRepository;

public interface TodoRepository extends JpaRepository<Todo, Long> {
}
```

Этим репозиторием управляет JPA Spring Data.

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
    --data '{"description":"configuration","details":"congratulations, you have set up JPA correctly!","done": "true"}' \
    http://127.0.0.1:8080
```

Эта команда должна возвращать созданный элемент:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up JPA correctly!","done":true}
```

Затем извлеките данные, используя новый запрос cURL:

```bash
curl http://127.0.0.1:8080
```

Эта команда вернет список элементов todo, включая созданный вами элемент:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up JPA correctly!","done":true}]
```
