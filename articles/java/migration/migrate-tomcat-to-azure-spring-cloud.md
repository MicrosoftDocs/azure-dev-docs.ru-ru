---
title: Миграция приложений Tomcat в Azure Spring Cloud
description: Из этого руководства вы узнаете, что следует учитывать при миграции существующего приложения Tomcat в Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 6/16/2020
ms.openlocfilehash: 7b8a29c2769b3c4b04a40053d0470bfc6b1a0cca
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275188"
---
# <a name="migrate-a-tomcat-application-to-azure-spring-cloud"></a>Миграция приложения Tomcat в Azure Spring Cloud

Из этого руководства вы узнаете, что следует учитывать при миграции существующего приложения Tomcat для выполнения в Azure Spring Cloud.

## <a name="pre-migration"></a>Подготовка к миграции

Чтобы обеспечить успешную миграцию, перед ее началом выполните шаги оценки и инвентаризации, описанные в следующих разделах.

### <a name="switch-to-a-supported-platform"></a>Переход на поддерживаемую платформу

В Azure Spring Cloud поддерживаются определенные версии Java SE. Чтобы обеспечить совместимость, перенесите приложение в одну из поддерживаемых версий текущей среды, прежде чем продолжать. Обязательно полностью протестируйте готовую конфигурацию. Используйте в этих тестах последний стабильный выпуск дистрибутива Linux.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="identify-the-application-builddependency-system"></a>Определение системы зависимостей и сборки приложения

Определите, какие средства используются для сборки или упаковки приложения, включая скачивание всех зависимостей.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

### <a name="inventory-secrets"></a>Проверка секретов

#### <a name="passwords-and-secure-strings"></a>Пароли и защищенные строки

Проверьте все свойства и файлы конфигурации на рабочих серверах на наличие секретных строк и паролей. Обязательно проверьте файлы *server.xml* и *context.xml* в каталоге *$CATALINA_BASE/conf*. Кроме того, в приложении в *META-INF/context.xml* есть файлы конфигурации, содержащие пароли или учетные данные.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

### <a name="determine-whether-your-application-contains-os-specific-code"></a>Определение того, содержит ли приложение код, зависящий от ОС

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="determine-whether-tomcat-is-connected-to-a-web-server"></a>Определение того, установлено ли подключение между Tomcat и веб-сервером

Tomcat можно подключить к статическому веб-серверу, например Apache, через соединитель Tomcat, например `mod_jk`. Проверьте файл `workers.properties` в каталоге `conf`, чтобы определить, установлено ли такое подключение.

### <a name="special-cases"></a>Особые случаи

В некоторых рабочих сценариях может потребоваться внести дополнительные изменения. Также могут применяться дополнительные ограничения. Хотя такие сценарии могут использоваться нечасто, важно убедиться, что приложение в них не задействовано или что они правильно реализуются.

#### <a name="determine-if-the-application-uses-filters"></a>Определение того, использует ли приложение фильтры

Проверьте, есть ли в файле *web.xml* [настроенные фильтры](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#Expires_Filter/Basic_configuration_sample).

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud](includes/determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud.md)]

#### <a name="determine-whether-non-http-connectors-are-used"></a>Определение того, используются ли соединители, отличающиеся от HTTP

Azure Spring Cloud поддерживает только HTTP-подключения на одном ненастраиваемом HTTP-порте. Если приложению требуются дополнительные порты или протоколы, не используйте Azure Spring Cloud.

Чтобы определить соединители HTTP, используемые приложением, найдите элементы `<Connector>` в файле *server.xml* в конфигурации Tomcat.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Определение того, используется ли отслеживание сеансов SSL

В Azure Spring Cloud сеанс SSL завершится, прежде чем будет достигнут код приложения, поэтому вы не сможете использовать [отслеживание сеансов SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL). Вместо этого используйте [Spring Session](https://docs.spring.io/spring-session/docs/current/reference/html5/index.html).

#### <a name="determine-whether-tomcat-realms-are-used"></a>Определение того, используются ли области Tomcat

В Azure Spring Cloud необходимо использовать Spring Security вместо областей Tomcat. Проверьте файл *server.xml*, чтобы выполнить инвентаризацию [настроенных областей](https://tomcat.apache.org/tomcat-9.0-doc/realm-howto.html#Configuring_a_Realm).

#### <a name="determine-whether-servlet-filters-are-used"></a>Определение того, используются ли фильтры сервлета

Проверьте, есть ли в файле *web.xml* приложения элементы `<filter>`. Список доступных фильтров см. в [документации по фильтрам Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html).

## <a name="migration"></a>Миграция

### <a name="remove-connection-to-web-server-if-present"></a>Удаление существующего подключения к веб-серверу

Если для Tomcat установлено подключение к статическому веб-серверу (как правило, это Apache) через `mod_jk`, разорвите это подключение, чтобы обеспечить выполнение Tomcat в качестве автономного сервера, создав при необходимости веб-перенаправления со Standard Server. Рассмотрите возможность миграции статического веб-содержимого в службу хранилища Azure с помощью сети доставки содержимого Azure (CDN). См. руководство по [размещению статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website) и [ Интеграция учетной записи хранения Azure с Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

### <a name="update-to-tomcat-9"></a>Обновление до Tomcat 9

Если текущее приложение выполняется в версии до Tomcat 9, выполните миграцию на Tomcat 9 и убедитесь, что приложение полностью работоспособно. Дополнительные сведения см. в [руководстве по миграции на Tomcat 9](http://tomcat.apache.org/migration-9.html).

### <a name="switch-to-maven-or-gradle"></a>Переключение на Maven или Gradle

Для работы Spring Boot и Spring Cloud требуются Maven или Gradle для управления сборкой и зависимостями. Если приложение использует другую систему управления сборкой и зависимостями, переключитесь на текущую версию [Maven](https://maven.apache.org/download.cgi), прежде чем продолжать работу. Хотя Gradle также поддерживается, мы будем использовать Maven на всех шагах этого руководства. Если вы решили использовать Gradle, адаптируйте инструкции соответствующим образом.

Создайте [файл POM](https://maven.apache.org/pom.html) для своего приложения и убедитесь, что созданное приложение полностью работоспособно.

### <a name="migrate-to-spring-boot"></a>Миграция в Spring Boot

В следующей таблице приведена сводка по требуемым миграциям и изменениям кода для миграции приложения Tomcat в Spring Boot и, следовательно, в Azure Spring Cloud. Если в приложении используется какой-либо элемент, указанный в столбце "Прежняя версия", его нужно заменить соответствующим элементом, указанным в столбце "Минимальная миграция" или "Рекомендуемая миграция".

|Прежняя версия | Где проверить |Минимальная миграция |Рекомендуемая миграция|
|---|---|---|---|
|[JDBC через DataSource](https://docs.oracle.com/javase/tutorial/jdbc/basics/connecting.html)|*server.xml*|[Источник данных Spring Data](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-sql) с [JdbcTemplate](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template)|Рассмотрите возможность использования Spring Data и JPA, если это возможно|
|Сервлеты |*web.xml*|Включите [инициализацию контекста сервлета](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) и добавьте заметки `@WebServlet`|Перепишите как [контроллеры Spring MVC (с использованием `@RestController`](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller))
|Фильтры |*web.xml*| Включите [инициализацию контекста сервлета](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) и [добавьте заметки `@WebFilter`](https://docs.oracle.com/javaee/7/api/javax/servlet/annotation/WebFilter.html) |Сохраните как минимальную миграцию|
|Java Server Pages (JSP)|Содержимое файла WAR и *web.xml*|[Представления JSP для Spring MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view-jsp)|Разместите слой представления отдельно|
|Java Message Service (JMS)|*server.xml*|Создайте экземпляр фабрики подключений в качестве [Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)|Используйте [Spring JMS](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/integration.html#jms-using)
|Статическое содержимое (изображения, файлы JavaScript и т. д.) в файле WAR|Каталог статического содержимого (обычно */static*, */public* или */resources*)|Переместите содержимое в */src/main/resources*|См. [рекомендации по использованию статического содержимого перед миграцией](#read-only-static-content)
|Статическое содержимое (изображения, файлы JavaScript и т. д.) за пределами файла WAR|Путь в локальной файловой системе|Переместите содержимое в *src/main/resources* Найдите исходный код с прописанными путями и измените на [ClassPathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ClassPathResource.html) |См. [рекомендации по использованию статического содержимого перед миграцией](#read-only-static-content)

1. Если приложение использует библиотеки, внедренные через ресурсы JNDI (например, драйверы JDBC), добавьте эти библиотеки как зависимости в файл POM. Удалите библиотеки с сервера Tomcat (как правило, это каталог *tomcat/lib*) и убедитесь, что приложение полностью работоспособно, прежде чем продолжать работу.

1. Добавьте в файл POM родительский элемент Spring Boot. Сведения о [создании файла POM](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-pom) см. в документации по Spring Boot.

1. Добавьте starter для Spring Boot Tomcat в качестве зависимости в файл POM:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

    Хотя это было приложение Tomcat, не добавляйте `war` в качестве целевой упаковки.

1. Измените источники данных Tomcat на источники данных Spring. [Настройте источники данных Spring](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-configure-a-datasource) для всех баз данных, используемых приложением. Если код выполняет прямые запросы SQL, измените его, чтобы [использовать JdbcTemplate](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template). Сведения о дополнительных возможностях доступа к данным, включая управление транзакциями и использование инструментария CRUD, см. в документации по [Spring Framework](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#jdbc) и [Spring Data](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference).

1. Хотя реализацию сервлета можно реализовать во [внедренном контейнере сервлета](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container), делать это не рекомендуется. Вместо этого замените [реализации сервлета](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html) [контроллерами REST](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller) Spring. Если приложение использует платформу, отличающуюся от Spring MVC, перейдите на Spring MVC. Сведения см. в [справочнике по контроллерам Spring MVC с заметками](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc-controller).

1. Повторно создайте все остальные зависимости JNDI с помощью [объектов Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection). Используйте идиоматические механизмы Spring, такие как [Spring JMS](https://spring.io/guides/gs/messaging-jms/) для обмена сообщениями.

1. Измените область Tomcat на [Spring Security](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-filters-review). Рассмотрите возможность использования Azure Active Directory для управления авторизацией с помощью [starter Spring Boot для Active Directory](/azure/developer/java/spring-framework/spring-boot-starters-for-azure#azure-active-directory).

1. Повторно создайте фильтры сервлета, настроенные в *web.xml* с помощью [объектов Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-as-spring-bean) или [проверки путей к классам](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-using-scanning).

1. Если приложение содержит статическое содержимое или ссылается на него, например изображения или файлы JavaScript, эти файлы следует переместить в *src/main/resources* в исходном коде проекта. После перемещения обновите исходный код, чтобы удалить все ссылки на локальную файловую систему. Для доступа к этим файлам используйте класс `ClassPathResource` Spring.

Протестируйте приложение, выполнив `mvn spring-boot:run`. Прежде чем продолжать работу, убедитесь, что полученное приложение полностью работоспособно.

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Действия после миграции

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
