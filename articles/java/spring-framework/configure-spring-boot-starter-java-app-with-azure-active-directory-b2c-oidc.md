---
title: Использование начального приложения Spring Boot с Azure Active Directory B2C
description: Сведения о настройке приложения Spring Boot Initializr с помощью начального приложения Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: edburns
ms.date: 10/23/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: aa2d60f969895acbcb56f74e909993ceb9b0d7d5
ms.sourcegitcommit: 5c7f5fef798413b1a304cc9ee31c8518b73f27eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066330"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>Руководство. Защита приложения Java с использованием starter Spring Boot для Azure Active Directory B2C

В этой статье описывается, как с помощью [Spring Initializr](https://start.spring.io/) создать начальное приложение Java Spring Boot для Azure Active Directory (Azure AD).

В этом руководстве описано следующее:

> [!div class="checklist"]
> * создание приложения Java с помощью Spring Initializr;
> * настройка Azure Active Directory B2C;
> * защита приложения с помощью классов и аннотаций Spring Boot;
> * сборка и тестирование приложения Java.

[Azure Active Directory](https://azure.microsoft.com/services/active-directory) представляет собой решение Майкрософт для корпоративной идентификации в облачном масштабе. [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) дополняет набор функций Azure Active Directory возможностью управлять доступом клиентов, потребителей и пользователей к приложениям B2C.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) запись, прежде чем начать работу.
* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

2. Укажите значения в соответствии с инструкциями в этом руководстве. Обратите внимание, что метки и макет могут отличаться от представленных здесь изображений.

    * В поле **Project** (Проект) выберите элемент **Maven Project** (Проект Maven).
    * В поле **Language** (Язык) выберите элемент **Java**.
    * В поле **Spring Boot** выберите **2.3.4**.
    * В полях **Group** (Группа), **Artifact** (Артефакт) и **Name** (Имя) введите одно и то же строковое значение с кратким описанием проекта. Возможно, пользовательский интерфейс автоматически заполнит часть этих значений по мере ввода.
    * На панели **Dependencies** (Зависимости) выберите элемент **Add Dependencies** (Добавить зависимости). Через предоставленный пользовательский интерфейс добавьте зависимости **Spring Web** и **Spring Security**.

   ![Заполнение значений для создания проекта](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/fill-in-the-values-to-generate-the-project.png)

    > [!NOTE]
    > Spring Initializr использует Java 11 в качестве версии по умолчанию. Для использования starter Spring Boot, описанных в этом разделе, необходимо выбрать Java 8.


3. Щелкните элемент **Generate Project** (Создать проект), затем скачайте проект в папку на локальном компьютере. Переместите скачанный файл в каталог, имя которого совпадает с именем проекта, и распакуйте этот файл. Макет файла должен быть похож на представленный ниже пример, но с указанным вами значением для параметра **Group** (Группа) вместо указанного здесь `yourProject`.

    ```
    .
    ├── HELP.md
    ├── mvnw
    ├── mvnw.cmd
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── yourProject
        │   │       └── yourProject
        │   │           └── YourProjectApplication.java
        │   └── resources
        │       ├── application.properties
        │       ├── static
        │       └── templates
        └── test
            └── java
                └── yourProject
                    └── yourProject
                        └── YourProjectApplicationTests.java
    ```

## <a name="create-and-initialize-an-azure-active-directory-instance"></a>Создание и инициализация экземпляра Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Создание экземпляра Active Directory

1. Войдите на сайт <https://portal.azure.com>.

2. Щелкните команду **+ Создать ресурс** , а затем выберите элементы **Удостоверение** и **Показать все**. Выполните поиск по фразе **Azure Active Directory B2C**.

    ![Создание экземпляра Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. Нажмите кнопку **создания**.

    ![Получение имени клиента B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. Выберите **Создать B2C-клиент Azure AD**.

    ![Создание клиента Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. Введите подходящие значения в полях **Имя организации** и **Первоначальное доменное имя** , а затем щелкните элемент **Создать**.

    ![Выбор экземпляра Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. После создания клиента в Active Directory перейдите к новому каталогу. Кроме того, можно выполнить поиск по строке `b2c` и выбрать элемент **Azure AD B2C**.

    ![Поиск экземпляра Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Регистрация приложения для приложения Spring Boot

1. В области **Управление** слева выберите **Регистрация приложений** и **Новая регистрация**.

   ![Снимок экрана: портал Azure с окном "Регистрация приложений Azure AD B2C"](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. В поле **Имя** введите значение **Группа** , которое вы использовали выше, задайте для параметра **URI перенаправления (рекомендуется)** значение *http://localhost:8080/home* и щелкните **Зарегистрировать**.

   ![Настройка регистрации нового приложения](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c4-n.png)

3. Вернитесь на панель **Управление** , щелкните **Приложения (прежние версии)** , а затем выберите созданное имя приложения.

   ![Обновление приложения](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c5-n.png)

4. Выберите **Свойства** и задайте для параметра **Разрешить неявный поток** значение **Да**.
   
5. Сохраните в других полях значения по умолчанию.
    
6. Нажмите **Сохранить**. Подготовка приложения может занять некоторое время.
    
   ![Обновление ключа приложения](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c6-n.png)

7. В области **Общие** выберите **Ключи** , а затем щелкните **Создать ключ**.

8. Задайте для параметра **Ключ приложения** значение, указанное ранее в поле **Группа**.

9. Щелкните **Сохранить**. Дождитесь, пока ключ появится в разделе ключей приложения, затем скопируйте его для использования далее в этой статье.

    > [!NOTE]
    > Если вы закроете раздел **Ключи** , при последующих входах значение ключа отображаться не будет. В таком случае придется создать еще один ключ и скопировать его для дальнейшего использования.
    > Иногда созданный ключ будет содержать символы, которые сложно включить в файл *application.yml* , например обратную косую черту или обратный апостроф. В этом случае просто удалите такой ключ и создайте новый.

    ![Создание секрета](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

10. Щелкните **Обзор**.

11. В разделе **Политики** на панели слева выберите элемент **Потоки пользователя** , а затем команду **Создать поток пользователя**.

12. Теперь вам нужно отложить работу с этим руководством, выполнить инструкции из другого руководства и вернуться к этому руководству по завершении работы. При переходе к другому руководству следует помнить о некоторых вещах.

    * Начните с этапа, на котором предлагается выбрать действие **Создать поток пользователя**.
    * При каждом упоминании `webapp1` в этом руководстве заменяйте его тем значением, которое вы указали в поле **Group** (Группа).
    * Когда вы выбираете утверждения, которые должны возвращаться из потоков, обязательно укажите **Display Name** (Отображаемое имя). Без этого утверждения не будет работать приложение, которое вы создадите по инструкциям в этом руководстве.
    * Когда вам предлагается выполнить потоки пользователя, указанный выше URL-адрес перенаправления еще не активен. Это не помешает вам выполнить потоки, но перенаправление не будет успешным. Это ожидаемое поведение.
    * Когда вы дойдете до этапа "Дальнейшие действия", вернитесь к этому руководству.

    Выполните все действия, описанные в статье [Руководство. Создание потоков пользователей в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-user-flows), чтобы создать потоки пользователей "Регистрация и входа", "Изменение профиля" и "Сброс пароля".

    Azure AD B2C поддерживает локальные учетные записи, а также поставщики удостоверений социальных сетей. Пример создания поставщика удостоверений GitHub см. в статье [Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C](/azure/active-directory-b2c/identity-provider-github).

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

Итак, вы создали экземпляр Azure AD B2C и несколько потоков пользователей, которые позволят вам подключить приложение Spring к этому экземпляру Azure AD B2C.

1. В сеансе командной строки перейдите с помощью команды cd в каталог, куда вы распаковали содержимое ZIP-файла, скачанного из Spring Initializr.

2. Перейдите в родительскую папку проекта и откройте файл проекта Maven *pom.xml* в текстовом редакторе.

3. Добавьте в *pom.xml* зависимости для применения защиты Spring OAuth2:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        <version>See Below</version>
    </dependency>
    ```

    Для параметра `azure-active-directory-b2c-spring-boot-starter` укажите последнюю доступную версию. Вы можете найти ее на сайте [mvnrepository.com](https://mvnrepository.com/ artifact/com.microsoft.azure/azure-active-directory-spring-boot-starter). На момент этого обновления последняя версия имела номер `2.3.5`.

    Для параметра `spring-boot-starter-thymeleaf` укажите версию, которая соответствует выбранной версии Spring Boot, например `2.3.4.RELEASE`.

    Для параметра `thymeleaf-extras-springsecurity5` укажите последнюю доступную версию. Вы можете найти ее на сайте [mvnrepository.com](https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity5). На момент написания этой статьи последняя версия имела номер `3.0.4.RELEASE`.

4. Сохраните и закройте файл *pom.xml*.

    * Проверьте правильность всех зависимостей, выполнив `mvn -DskipTests clean install`. Если не отобразится сообщение `BUILD SUCCESS`, устраните неполадку и решите проблему, прежде чем продолжить работу.

5. Перейдите к папке *src/main/resources* проекта и создайте файл *application.yml* в текстовом редакторе.

6. Задайте параметры для регистрации вашего приложения, используя созданные ранее значения. Например:

    ```yaml
    azure:
      activedirectory:
        b2c:
          tenant: ejb0518domain
          client-id: 11111111-1111-1111-1111-1111111111111111
          client-secret: '<yourAppKey>'
          reply-url: http://localhost:8080/home
          logout-success-url: http://localhost:8080/home
          user-flows:
            sign-up-or-sign-in: B2C_1_signupsignin1
            profile-edit: B2C_1_profileediting1
            password-reset: B2C_1_passwordreset1
    ```

    Обратите внимание, что значение `client-secret` заключено в одинарные кавычки. Это необходимо потому, что значение `<yourAppKey>` почти наверняка будет содержать некоторые символы, которые в формате YAML обязательно заключаются в одинарные кавычки.

    > [!NOTE]
    > На момент написания этой статьи для интеграции Active Directory B2C со Spring в файле *application.yml* можно указывать следующие значения:
    >
    > ```
    > azure:
    >   activedirectory:
    >     b2c:
    >       tenant:
    >       oidc-enabled:
    >       client-id:
    >       client-secret:
    >       reply-url:  # should be absolute url.
    >       logout-success-url:
    >       user-flows:
    >         sign-up-or-sign-in:
    >         profile-edit: # optional
    >         password-reset: # optional
    > ```
    >
    > Файл *application.yml* доступен в [примере Azure Active Directory B2C для Spring Boot](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/application.yml) на сайте GitHub.

7. Сохраните и закройте файл *application.yml*.

8. Создайте папку с именем *controller* в *src/main/java/<yourGroupId>/<yourGroupId>* , заменив `<yourGroupId>` значением, которое вы указали в поле **Group** (Группа).

9. Создайте файл Java с именем *WebController.java* в папке *controller* и откройте его в текстовом редакторе.

10. Введите в файл следующий код, изменив `yourGroupId` соответствующим образом, а затем сохраните и закройте файл:

    ```java
    package yourGroupId.yourGroupId.controller;

    import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;

    @Controller
    public class WebController {

        private void initializeModel(Model model, OAuth2AuthenticationToken token) {
            if (token != null) {
                final OAuth2User user = token.getPrincipal();

                model.addAttribute("grant_type", user.getAuthorities());
                model.addAllAttributes(user.getAttributes());
            }
        }

        @GetMapping(value = "/")
        public String index(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }

        @GetMapping(value = "/greeting")
        public String greeting(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "greeting";
        }

        @GetMapping(value = "/home")
        public String home(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }
    }
    ```

    Так как каждый метод в контроллере вызывает `initializeModel()`, а этот метод вызывает `model.addAllAttributes(user.getAttributes());`, любая HTML-страница в *src/main/resources/templates* может получить доступ к любому из этих атрибутов, таким как `${name}`, `${grant_type}`или `${auth_time}`. Значения, возвращаемые из `user.getAttributes()`, фактически являются утверждениями `id_token` для проверки подлинности. Полный список доступных утверждений приведен в статье [Маркеры идентификаторов платформы удостоверений Майкрософт](/azure/active-directory/develop/id-tokens#payload-claims).

11. Создайте папку с именем *security* в *src/main/java/<yourGroupId>/<yourGroupId>* , заменив `yourGroupId` значением, которое вы указали в поле **Group** (Группа).

12. Создайте файл Java с именем *WebSecurityConfiguration.java* в папке *security* и откройте его в текстовом редакторе.

13. Введите в файл следующий код, изменив `yourGroupId` соответствующим образом, а затем сохраните и закройте файл:

    ```java
    package yourGroupId.yourGroupId.security;

    import com.microsoft.azure.spring.autoconfigure.b2c.AADB2COidcLoginConfigurer;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

    @EnableWebSecurity
    public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

        private final AADB2COidcLoginConfigurer configurer;

        public WebSecurityConfiguration(AADB2COidcLoginConfigurer configurer) {
            this.configurer = configurer;
        }

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .apply(configurer)
            ;
        }
    }
    ```

14. Скопируйте файлы *greeting.html* и *home.html* из [примера Azure AD B2C для Spring Boot](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/templates) в папку *src/main/resources/templates*. Затем замените `${your-profile-edit-user-flow}` и `${your-password-reset-user-flow}` именами созданных ранее потоков пользователей.

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*.

2. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

    > [!NOTE]
    > Очень важно, чтобы время системных часов на том устройстве, где выполняется локальное приложение Spring Boot, было точным. При использовании OAuth 2.0 допускается лишь очень малое отклонение по времени. Даже три минуты неточности могут привести к появлению при входе ошибок следующего вида: `[invalid_id_token] An error occurred while attempting to decode the Jwt: Jwt used before 2020-05-19T18:52:10Z`. На момент написания этой статьи сайт [time.gov](https://time.gov/) предоставлял информацию о том, насколько показания ваших часов отличаются от реального времени. Приложение успешно выполнено с отклонением в +0,019 секунд.

    ```shell
    mvn -DskipTests clean package
    mvn -DskipTests spring-boot:run
    ```

3. Скомпилировав и запустив приложение с помощью Maven, перейдите в веб-браузере по адресу `http://localhost:8080/`, после чего вы будете перенаправлены на страницу входа.

    ![Страница входа](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. Выберите ссылку с текстом, обозначающим процесс входа. Вы перейдете на страницу Azure AD B2C, где можете начать проверку подлинности.

5. Войдя в приложение, вы увидите пример `home page` в браузере.

    ![Успешный вход в приложение](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>Сводка

В рамках этого руководства вы создали веб-приложение Java с использованием начального приложения Spring для Azure Active Directory B2C, настроили клиент Azure AD B2C, зарегистрировали в нем созданное приложение, а затем настроили это приложение для использования аннотаций и классов Spring для защиты веб-приложения.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, используйте [портал Azure](https://portal.azure.com/), чтобы удалить ресурсы, созданные в этой статье во избежание непредвиденных расходов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](./index.yml)