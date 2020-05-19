---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 34701a7d3fd85fea412be859dc21823df2dde053
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990196"
---
#### <a name="identify-spring-boot-versions"></a>Определение версий Spring Boot

Изучите зависимости каждого переносимого приложения, чтобы определить его версию Spring Boot.

##### <a name="maven"></a>Maven

В проектах Maven версию Spring Boot обычно можно узнать в элементе `<parent>` файл POM:

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

##### <a name="gradle"></a>Gradle

В проектах Gradle версию Spring Boot обычно можно узнать в элементе в разделе `plugins`. Она указана в качестве версии подключаемого модуля `org.springframework.boot`.

```gradle
plugins {
  id 'org.springframework.boot' version '2.2.6.RELEASE'
  id 'io.spring.dependency-management' version '1.0.9.RELEASE'
  id 'java'
}
```
