---
title: Как использовать начальное приложение Spring Boot с Azure Active Directory B2C
description: Сведения о настройке приложения Spring Boot Initializer с помощью начального приложения Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
editor: panli
ms.assetid: ''
ms.author: panli
ms.date: 02/28/2019
ms.devlang: java
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: c0c7ec20c4e4a9472b8653fd3940740238a7420c
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68283475"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>Руководство по защите приложения Java с использованием начального приложения Spring Boot для Azure Active Directory B2C.

## <a name="overview"></a>Обзор

В этой статье описывается, как с помощью [Spring Initializr](https://start.spring.io/) создать начальное приложение Java Spring Boot для Azure Active Directory (Azure AD).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание приложения Java с помощью Spring Initializr;
> * настройка Azure Active Directory B2C;
> * защита приложения с помощью классов и аннотаций Spring Boot;
> * сборка и тестирование приложения Java.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

2. Укажите, что необходимо создать проект **Maven** с помощью **Java**, введите имя **группы** и **артефакта** вашего приложения, а затем выберите модуль **Интернет** и **Безопасность** Spring Initializr.

   ![Указание имен группы и артефакта](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/SI.png)


3. При появлении запроса щелкните `Generate Project` и скачайте проект на локальный компьютер.

## <a name="create-azure-active-directory-instance"></a>Создание экземпляра Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Создание экземпляра Active Directory

1. Войдите на сайт <https://portal.azure.com>.

2. Щелкните **Создать ресурс**, а затем выберите **Удостоверение** и **Azure Active Directory B2C**.

   ![Создание экземпляра Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/AZ1.png)

3. Укажите **имя организации** и **первоначальное доменное имя**, а затем запишите **имя домена** как `${your-tenant-name}` и щелкните **Создать**.

   ![Получение имени клиента B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/AZ5.png)

4. Выберите имя учетной записи в правом верхнем углу панели инструментов на портале Azure и щелкните **Переключение каталога**.

   ![Выбор экземпляра Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/AZ2.png)

5. Выберите новый экземпляр Azure Active Directory в раскрывающемся меню.

   ![Выбор экземпляра Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/AZ3.png)

6. Найдите `b2c` и щелкните службу `Azure AD B2C`.

   ![Поиск экземпляра Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/AZ4.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Регистрация приложения для приложения Spring Boot

1. В меню портала выберите **Azure AD B2C**, щелкните **Приложения**, а затем выберите **Добавить**.

   ![Регистрация приложения](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/B2C1.png)

2. Укажите **имя** приложения, введите `http://localhost:8080/home` в поле **URL-адрес ответа**, запишите значение параметра **Идентификатор приложения** как `${your-client-id}`, а затем щелкните **Сохранить**.

   ![Добавление URL-адреса ответа приложения](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/B2C2.png)

3. В приложении выберите **Ключи**, щелкните **Создать ключ**, чтобы создать `${your-client-secret}`, а затем выберите **Сохранить**.

4. Слева выберите **Потоки пользователей**, а затем щелкните **Создать поток пользователя**.

   ![Создание потока пользователя](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/B2C3.png)

5. Выберите **Sign up or in** (Регистрация или вход), **Profile editing** (Изменение профиля) и **Сброс пароля**, чтобы создать соответствующие потоки пользователей. Укажите **имя** потока пользователя, а также **атрибуты пользователя и утверждения** и щелкните **Создать**.

   ![Настройка потока пользователей](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/B2C4.png)

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

1. Распакуйте архив с файлами проекта, который вы создали и скачали в каталог ранее в рамках этого руководства.

2. Перейдите в родительскую папку проекта и откройте файл проекта Maven `pom.xml` в текстовом редакторе.

3. Добавьте зависимости для защиты с помощью Spring OAuth2 в `pom.xml`:

   ```xml
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
       <version>2.1.6.M2</version>
   </dependency>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   <dependency>
       <groupId>org.thymeleaf.extras</groupId>
       <artifactId>thymeleaf-extras-springsecurity5</artifactId>
   </dependency>
   ```

4. Сохраните и закройте файл *pom.xml*.

5. В папке *src/main/resources* проекта откройте файл *application.yml* в текстовом редакторе.

6. Задайте параметры для регистрации вашего приложения, используя созданные ранее значения. Например:

   ```yaml
   azure:
     activedirectory:
       b2c:
         tenant: ${your-tenant-name}
         client-id: ${your-client-id}
         client-secret: ${your-client-secret}
         reply-url: ${your-reply-url-from-aad} # should be absolute url.
         logout-success-url: ${you-logout-success-url}
         user-flows:
           sign-up-or-sign-in: ${your-sign-up-or-in-user-flow}
           profile-edit: ${your-profile-edit-user-flow}     # optional
           password-reset: ${your-password-reset-user-flow} # optional
   ```
   Описание

   | Параметр | ОПИСАНИЕ |
   |---|---|
   | `azure.activedirectory.b2c.tenant` | Содержит имя `${your-tenant-name` AD B2C, указанное ранее. |
   | `azure.activedirectory.b2c.client-id` | Содержит `${your-client-id}` из приложения, указанный ранее. |
   | `azure.activedirectory.b2c.client-secret` | Содержит `${your-client-secret}` из приложения, указанный ранее. |
   | `azure.activedirectory.b2c.reply-url` | Содержит один из **URL-адресов ответа** из приложения, указанных ранее. |
   | `azure.activedirectory.b2c.logout-success-url` | Указывает URL-адрес после успешного выхода из приложения. |
   | `azure.activedirectory.b2c.user-flows` | Содержит имена потоков пользователей, созданных ранее.

   > [!NOTE]
   > 
   > Полный список значений, доступных в файле *application.yml*, см. в [примере приложения Spring Boot для Azure Active Directory B2C][примере приложения Spring Boot для AAD B2C] на сайте GitHub.
   >

7. Сохраните и закройте файл *application.yml*.

8. Создайте папку с именем *controller* в исходной папке Java для приложения.

9. Создайте файл Java с именем *HelloController.java* в папке *controller* и откройте его в текстовом редакторе.

10. Вставьте следующий код, а затем сохраните и закройте файл:

    ```java
    package sample.aad.controller;
    
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

11. Создайте папку с именем *security* в исходной папке Java для приложения.

12. Создайте файл Java с именем *WebSecurityConfig.java* в папке *security* и откройте его в текстовом редакторе.

13. Вставьте следующий код, а затем сохраните и закройте файл:

    ```java
    package sample.aad.security;
    
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
14. Скопируйте `greeting.html` и `home.html` из [примера приложения Spring Boot для Azure AD B2C](https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-b2c-oidc-spring-boot-sample/src/main/resources/templates), а затем замените `${your-profile-edit-user-flow}` и `${your-password-reset-user-flow}` именем потока пользователя, созданного ранее.

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*.

2. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

3. Скомпилировав и запустив приложение с помощью Maven, перейдите в веб-браузере по адресу <http://localhost:8080/>, после чего вы будете перенаправлены на страницу входа.

   ![Страница входа](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/LO1.png)

4. Щелкните ссылку с именем потока пользователя `${your-sign-up-or-in}`, после чего вы будете перенаправлены в Azure AD B2C, чтобы начать процесс проверки подлинности.

   ![Вход в Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/LO2.png)

4. Войдя в приложение, вы увидите пример `home page` в браузере.

   ![Успешный вход в приложение](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/LO3.png)

## <a name="summary"></a>Сводка

В этом учебнике вы создали веб-приложение Java с использованием начального приложения Spring Boot для Azure Active Directory B2C, настроили клиент Azure AD B2C, зарегистрировали в нем созданное приложение, а затем настроили это приложение для использования аннотаций и классов Spring с целью защиты веб-приложения.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)
