---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 34701a7d3fd85fea412be859dc21823df2dde053
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990195"
---
#### <a name="identify-spring-boot-versions"></a>Spring Boot 버전 확인

마이그레이션되는 각 애플리케이션의 종속성을 검사하여 해당 Spring Boot 버전을 확인합니다.

##### <a name="maven"></a>Maven

Maven 프로젝트에서 Spring Boot 버전은 일반적으로 POM 파일의 `<parent>` 요소에 있습니다.

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

##### <a name="gradle"></a>Gradle

Gradle 프로젝트에서 Spring Boot 버전은 일반적으로 `plugins` 섹션에서 `org.springframework.boot` 플러그 인의 버전으로 찾을 수 있습니다.

```gradle
plugins {
  id 'org.springframework.boot' version '2.2.6.RELEASE'
  id 'io.spring.dependency-management' version '1.0.9.RELEASE'
  id 'java'
}
```
