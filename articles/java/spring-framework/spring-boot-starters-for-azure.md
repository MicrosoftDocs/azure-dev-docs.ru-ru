---
title: Начальные приложения Spring Boot для Azure
description: В этой статье описаны разные начальные приложения Spring Boot Starter, доступные для Azure.
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 48e1381761311a4ed9b5bd6f764af6f19f008fc1
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670020"
---
# <a name="spring-boot-starters-for-azure"></a>Начальные приложения Spring Boot для Azure

В этой статье описаны разные начальные приложения Spring Boot Starter для [Spring Initializr], которые предоставляют разработчикам Java функции интеграции для работы с Microsoft Azure.

![Spring Boot Starter для Azure][spring-boot-starters]

Сейчас для Azure доступны следующие начальные приложения Spring Boot:

* **[Служба поддержки Azure](#azure-support)**

   Предоставляет поддержку автоматической настройки для служб Azure, включая служебную шину, службу хранилища, Active Directory и т. д.

* **[Azure Active Directory](#azure-active-directory)**

   Предоставляет поддержку интеграции для Spring Security с Azure Active Directory для аутентификации.

* **[Azure Key Vault](#azure-key-vault)**

   Начальное приложение Spring Boot поддерживает интеграцию с секретами Azure Key Vault.

* **[Служба хранилища Azure](#azure-storage)**

   Spring Boot поддерживает службы хранилища Azure.

<a name="azure-support"></a>
## <a name="azure-support"></a>Поддержка Azure

Начальное приложение Spring Boot поддерживает автоматическую настройку служб Azure, таких как Служебная шина Azure, служба хранилища Azure, Active Directory, Cosmos DB, Key Vault и др.

Примеры использования разных функций Azure с этим начальным приложением, см. по следующей ссылке:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples>

Когда вы добавляете это начальное приложение в проект Spring Boot, в файл *pom.xml* вносятся следующие изменения:

* В элемент `<properties>` добавляется следующее свойство:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Стандартная зависимость `spring-boot-starter` заменяется следующим образом:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-spring-boot</artifactId>
   </dependency>
   ```

* В файл добавляется следующий раздел:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-active-directory"></a>
## <a name="azure-active-directory"></a>Azure Active Directory

Это начальное приложение Spring Boot поддерживает автоматическую настройку Spring Security, обеспечивая интеграцию с Azure Active Directory для аутентификации.

Примеры использования функций Azure Active Directory с этим начальным приложением, см. по следующей ссылке:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-spring-boot-sample>

Когда вы добавляете это начальное приложение в проект Spring Boot, в файл *pom.xml* вносятся следующие изменения:

* В элемент `<properties>` добавляется следующее свойство:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Стандартная зависимость `spring-boot-starter` заменяется следующим образом:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-active-directory-spring-boot-starter</artifactId>
   </dependency>
   ```

* В файл добавляется следующий раздел:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-key-vault"></a>
## <a name="azure-key-vault"></a>Azure Key Vault

Приложение Spring Boot Starter обеспечивает поддержку заметок со значениями Spring для интеграции с секретами Azure Key Vault.

Примеры использования функций Azure Key Vault, предоставляемых этим начальным приложением, см. по следующей ссылке:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-keyvault-secrets-spring-boot-sample>

Когда вы добавляете это начальное приложение в проект Spring Boot, в файл *pom.xml* вносятся следующие изменения:

* В элемент `<properties>` добавляется следующее свойство:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Стандартная зависимость `spring-boot-starter` заменяется следующим образом:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
   </dependency>
   ```

* В файл добавляется следующий раздел:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-storage"></a>
## <a name="azure-storage"></a>Хранилище Azure

Начальное приложение Spring Boot Starter обеспечивает поддержку интеграции Spring Boot для служб хранилища Azure.

Примеры использования функций службы хранилища Azure, предоставляемых этим начальным приложением, см. по следующей ссылке:

* [Как использовать Spring Boot Starter со службой хранилища Azure](configure-spring-boot-starter-java-app-with-azure-storage.md)

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-storage-spring-boot-sample>

Когда вы добавляете это начальное приложение в проект Spring Boot, в файл *pom.xml* вносятся следующие изменения:

* В элемент `<properties>` добавляется следующее свойство:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Стандартная зависимость `spring-boot-starter` заменяется следующим образом:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-storage-spring-boot-starter</artifactId>
   </dependency>
   ```

* В файл добавляется следующий раздел:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании [Spring Boot] в Azure см. [Spring в Azure].

Дополнительные сведения об использовании Java в Azure см. в статьях [Azure для разработчиков Java] и [Working with Azure DevOps and Java] (Работа с Azure DevOps и Java).

Справку по началу работы с собственными приложениями Spring Boot см. на странице **Spring Initializr**: https://start.spring.io/.

<!-- URL List -->

[Azure для разработчиков Java]: /azure/developer/java/
[Working with Azure DevOps and Java]: /azure/devops/ (Работа с Azure DevOps и Java)
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring в Azure]: /azure/developer/java/spring-framework/
[Spring Framework]: https://spring.io/
[Spring Initializr]: https://start.spring.io/

<!-- IMG List -->

[spring-boot-starters]: media/spring-boot-starters-for-azure/spring-boot-starters-cropped.png
