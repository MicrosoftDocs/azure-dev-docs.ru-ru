---
title: Использование образов Docker с JDK для разработки приложений Java в Azure
description: Узнайте, как использовать образы Docker с пакетом средств разработки Java (JDK) для Azure при помощи интерфейса командной строки.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d7647757f371baa0b6fd21bd51d6629c6e1e0e10
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812264"
---
# <a name="use-docker-with-a-java-development-kit-jdk-for-azure"></a>Использование Docker с JDK (Java Development Kit) в Azure 

Узнайте, как использовать Docker с пакетом SDK для Java (JDK) в Azure. Готовые образы Docker для Java 7, 8 и 11 доступны через [Docker Hub](https://hub.docker.com/_/microsoft-java-se).

В Docker Hub доступны сертифицированные образы контейнеров Docker для Zulu JDK, JRE и автономной среды JRE на основе нескольких базовых образов ОС.

* [JDK](https://hub.docker.com/_/microsoft-java-jdk).
* [JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Автономная среда JRE](https://hub.docker.com/_/microsoft-java-jre-headless).

## <a name="running-a-docker-image"></a>Запуск образа Docker

Образы Docker можно запускать с помощью синтаксиса `$ docker run mcr.microsoft.com/java/jdk:tag java`, как показано в примере ниже.

```cli
docker run mcr.microsoft.com/java/jdk:8u212-zulu-alpine java -version 
```

## <a name="creating-a-docker-image"></a>Создание образа Docker

Вы можете создать образ на основе официальных образов Майкрософт в Docker Hub, как показано в примерах ниже.

### <a name="create-a-docker-file"></a>Создание файла Docker

```cli
FROM mcr.microsoft.com/java/jdk:8u212-zulu-alpine 
  
RUN echo $' \
  
public class HelloWorld { \
   public static void main(String[] args) { \
      // Prints "Hello, World" in the terminal window. \
      System.out.println("Hello, World - From Microsoft Azure !!!"); \
   } \
}' > HelloWorld.java
  
RUN javac HelloWorld.java
  
CMD ["java", "HelloWorld"]
```

### <a name="build-a-docker-image"></a>Создание образа Docker

```cli
docker build -t hello-world
```

### <a name="run-the-new-image"></a>Запуск нового образа

```cli
docker run hello-world
```

Вы увидите такой результат:

```output
Hello World - From Microsoft Azure !!!
```
