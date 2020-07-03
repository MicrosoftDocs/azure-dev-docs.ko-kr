---
author: judubois
ms.date: 06/16/2020
ms.author: judubois
ms.openlocfilehash: 4186673ede494fc16012416d354d821df08e8810
ms.sourcegitcommit: 3b069f1f89492f7e7bc5952a14dbfdde71d1e576
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85107642"
---
`DemoApplication` 클래스 옆에 새 `Todo` Java 클래스를 만들고 다음 코드를 추가합니다.

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

이 클래스는 `todo` 테이블에 매핑된 도메인 모델로, JPA에 의해 자동으로 생성됩니다.

해당 클래스를 관리하려면 리포지토리가 필요합니다. 동일한 패키지에 새 `TodoRepository` 인터페이스를 정의합니다.

```java
package com.example.demo;

import org.springframework.data.jpa.repository.JpaRepository;

public interface TodoRepository extends JpaRepository<Todo, Long> {
}
```

이 리포지토리는 Spring Data JPA에서 관리하는 리포지토리입니다.

데이터를 저장하고 검색할 수 있는 컨트롤러를 만들어 애플리케이션을 완성합니다. 동일한 패키지에 `TodoController` 클래스를 구현하고 다음 코드를 추가합니다.

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

마지막으로, 다음 명령을 사용하여 애플리케이션을 중지하고 다시 시작합니다.

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션을 테스트할 때 cURL을 사용할 수 있습니다.

먼저 다음 명령을 사용하여 데이터베이스에 새 "todo" 항목을 만듭니다.

```bash
curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"configuration","details":"congratulations, you have set up JPA correctly!","done": "true"}' \
    http://127.0.0.1:8080
```

이 명령은 다음과 같이 생성된 항목을 반환해야 합니다.

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up JPA correctly!","done":true}
```

다음으로, 다음과 같이 새 cURL 요청을 사용하여 데이터를 검색합니다.

```bash
curl http://127.0.0.1:8080
```

이 명령은 다음과 같이 생성된 항목을 포함하여 "todo" 항목 목록을 반환합니다.

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up JPA correctly!","done":true}]
```
