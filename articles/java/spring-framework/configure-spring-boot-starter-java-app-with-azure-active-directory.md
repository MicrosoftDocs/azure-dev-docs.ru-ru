---
title: Как использовать приложение Spring Boot Starter с Azure Active Directory
description: Сведения о настройке приложения Spring Initializr с помощью начального приложения Azure Active Directory.
services: active-directory
documentationcenter: java
ms.date: 03/05/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: ff89152b5cbcd8c0abeff74ce75c4ba21528613e
ms.sourcegitcommit: e6cdb0ce11a8272195a0072c7c91cc9b7e89b0b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138834"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Руководство по Защита приложения Java с использованием начального приложения Spring Boot для Azure Active Directory

## <a name="overview"></a>Обзор

В этой статье описывается, как с помощью **[Spring Initializr]** создать начальное приложение Java Spring Boot для Azure Active Directory (Azure AD).

В этом руководстве описано следующее:

 * создание приложения Java с помощью Spring Initializr;
 * настройка Azure Active Directory;
 * защита приложения с помощью классов и аннотаций Spring Boot;
 * сборка и тестирование приложения Java.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что требуется создать проект **Maven** на **Java**, введите имя **группы** и **артефакта** для приложения.

   ![Присвоение имен группе и артефакту][create-spring-app-01]

1. Прокрутите вниз и добавьте **зависимости** для **Spring Web**, **Azure Active Directory** и **Spring Security**.

1. Внизу страницы нажмите кнопку **Создать**.

   ![Выбор начальных компонентов Security, Web и Azure Active Directory][create-spring-app-02]

1. При появлении запроса скачайте проект на локальный компьютер.

## <a name="create-azure-active-directory-instance"></a>Создание экземпляра Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Создание экземпляра Active Directory

1. Войдите на сайт <https://portal.azure.com>.

1. Щелкните **Создать ресурс**, а затем выберите **Удостоверение** и **Azure Active Directory**.

   ![Создание экземпляра Azure Active Directory][create-directory-01]

1. Укажите **имя организации** и **первоначальное доменное имя**. Скопируйте полный URL-адрес вашего каталога. Он вам понадобится, чтобы добавить учетные записи пользователей, как описывается далее в этом руководстве. (Например, `wingtiptoysdirectory.onmicrosoft.com`.) 

    Скопируйте полный URL-адрес вашего каталога. Он вам понадобится, чтобы добавить учетные записи пользователей, как описывается далее в этом руководстве. (Например, wingtiptoysdirectory.onmicrosoft.com.).

    По завершении нажмите кнопку **Создать**. Создание ресурса займет несколько минут.

   ![Указание имен Azure Active Directory][create-directory-02]

1. По завершении щелкните ссылку, чтобы получить доступ к новому каталогу.

   ![Выбор учетной записи Azure][create-directory-03]

1. Скопируйте **идентификатор клиента**. Это значение понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве.

   ![Копирование идентификатора клиента][create-directory-05]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Регистрация приложения для приложения Spring Boot

1. В меню на портале выберите **Регистрация приложений**, а затем щелкните **Зарегистрировать приложение**.

   ![Регистрация приложения][create-app-registration-01]

1. Выберите свое приложение и щелкните **Зарегистрировать**.

   ![Создание регистрации приложения][create-app-registration-02]

1. Когда появится страница регистрации приложения, скопируйте **идентификатор приложения** и **идентификатор клиента**. Эти значения понадобятся при настройке файла *application.properties*, как описывается далее в этом руководстве.

   ![Создание ключей зарегистрированного приложения][create-app-registration-03]

1. В области навигации слева щелкните **Сертификаты и секреты**.  Щелкните **Создать секрет клиента**.

   ![Создание ключей зарегистрированного приложения][create-app-registration-03-5]

1. Добавьте **описание** и выберите нужное значение в списке **Срок действия**.  Нажмите кнопку **Добавить**. Значение ключа заполнится автоматически.

   ![Указание параметров зарегистрированного приложения][create-app-registration-04]

1. Скопируйте и сохраните секрет ключа. Это значение понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве. (Вы не сможете получить это значение позже.)

   ![Указание параметров зарегистрированного приложения][create-app-registration-04-5]

1. Щелкните **Разрешения API** в области навигации слева. 

1. Щелкните элемент **Microsoft Graph** и установите флажки **Доступ к каталогу, аналогичный доступу вошедшего пользователя** и **Вход в систему и чтение профиля пользователя**. Щелкните **Предоставить разрешения...** и выберите **Да** при появлении запроса.

   ![Предоставление разрешений на доступ][create-app-registration-08]

1. На главной странице регистрации приложения выберите **Проверка подлинности** и щелкните **Добавить платформу**.  Затем щелкните **Веб-приложения**.

    ![Изменение URL-адресов ответа][create-app-registration-09]

1. Введите <http:<span></span>//localhost:8080/login/oauth2/code/azure> в качестве нового **URI перенаправления**, а затем нажмите кнопку **Настроить**.

    ![Добавление нового URL-адреса ответа][create-app-registration-10]

1. На главной странице регистрации приложения щелкните **Манифест**, затем установите для параметра `oauth2AllowImplicitFlow` значение `true` и нажмите кнопку **Сохранить**.

    ![Настройка манифеста приложения][create-app-registration-11]

    > [!NOTE]
    > 
    > См. подробнее о параметре `oauth2AllowImplicitFlow` и других параметрах приложения в статье [Манифест приложения Azure Active Directory][AAD app manifest]. 
    >

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Добавление учетной записи пользователя в каталог и в группу

1. На странице **Обзор** своего экземпляра службы Active Directory щелкните **Все пользователи**, а затем — **Новый пользователь**.

   ![Добавление новой учетной записи пользователя][create-user-01]

1. Когда появится область **Пользователь**, введите значения в поля **Имя пользователя** и **Имя**.  Затем нажмите кнопку **Создать**.

   ![Ввод сведений об учетной записи пользователя][create-user-02]

   > [!NOTE]
   > 
   > При вводе имени пользователя укажите URL-адрес каталога, ранее скопированный в рамках этого руководства. Например:
   >
   > `wingtipuser@wingtiptoysdirectory.onmicrosoft.com`
   > 

1. Выберите **Группы** и **Создать группу**, чтобы создать группу, которая будет использоваться для авторизации в приложении.

1. Затем щелкните **Нет выбранных участников**. (В рамках этого руководства мы создадим группу с именем *Users*.)  Найдите пользователя, созданного на предыдущем шаге.  Чтобы добавить пользователя в группу, нажмите кнопку **Выбрать**.  Затем нажмите кнопку **Создать**, чтобы создать группу.

   ![Выбор пользователя для группы][create-user-03]

1. Вернитесь в область **Пользователи**, выберите тестового пользователя и щелкните **Сброс пароля**, а затем скопируйте пароль. Он вам понадобится при входе в приложение, как описывается далее в этом руководстве. 

   ![Отображение пароля][create-user-04]

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

1. Распакуйте архив с файлами проекта, который вы создали и скачали в каталог ранее в рамках этого руководства.

1. Перейдите в родительскую папку проекта и откройте файл проекта Maven `pom.xml` в текстовом редакторе.

1. Добавьте зависимости для защиты с помощью Spring OAuth2 в `pom.xml`:

   ```xml
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-jose</artifactId>
   </dependency>
   ```

1. Сохраните и закройте файл *pom.xml*.

1. В папке *src/main/resources* проекта откройте файл *application.properties* в текстовом редакторе.

1. Задайте параметры для регистрации вашего приложения, используя созданные ранее значения. Например:

   ```yaml
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

   # Specifies your App Registration's Application ID:
   spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

   # Specifies your App Registration's secret key:
   spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.active-directory-groups=Users
   ```
   Где:

   | Параметр | Описание |
   |---|---|
   | `azure.activedirectory.tenant-id` | Содержит **идентификатор каталога** Active Directory, который вы скопировали ранее. |
   | `spring.security.oauth2.client.registration.azure.client-id` | Содержит **идентификатор приложения**, полученный после регистрации приложения. |
   | `spring.security.oauth2.client.registration.azure.client-secret` | Содержит **значение** ключа, полученное после регистрации приложения. |
   | `azure.activedirectory.active-directory-groups` | Содержит список групп Active Directory, используемых для авторизации. |

   > [!NOTE]
   > 
   > Полный список значений, доступных в файле *application.properties*, см. на [сайте GitHub][AAD Spring Boot Sample].
   >

1. Сохраните и закройте файл *application.properties*.

1. Создайте папку с именем *controller* в папке с исходным кодом Java для приложения, например: *src/main/java/com/wingtiptoys/security/controller*.

1. Создайте файл Java с именем *HelloController.java* в папке *controller* и откройте его в текстовом редакторе.

1. Вставьте следующий код, а затем сохраните и закройте файл:

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.access.prepost.PreAuthorize;
   import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
   import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
   import org.springframework.ui.Model;

   @RestController
   public class HelloController {
      @Autowired
      @PreAuthorize("hasRole('Users')")
      @RequestMapping("/")
      public String helloWorld() {
         return "Hello World!";
      }
   }
   ```
   > [!NOTE]
   > 
   > Имя группы, указываемое для метода `@PreAuthorize("hasRole('')")`, должно содержать одну из групп, указанных в поле `azure.activedirectory.active-directory-groups` в файле *application.properties*.
   > 
   > Для разных сопоставлений запросов можно указывать разные параметры авторизации, например:
   >
   > ``` java
   > public class HelloController {
   >    @Autowired
   >    @PreAuthorize("hasRole('Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('Group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('Group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```
   >    

1. Создайте папку с именем *security* в папке с исходным кодом Java для приложения, например: *src/main/java/com/wingtiptoys/security/security*.

1. Создайте файл Java с именем *WebSecurityConfig.java* в папке *security* и откройте его в текстовом редакторе.

1. Вставьте следующий код, а затем сохраните и закройте файл:

    ```java
    package com.wingtiptoys.security;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
    import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
    import org.springframework.security.oauth2.core.oidc.user.OidcUser;

    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Autowired
        private OAuth2UserService<OidcUserRequest, OidcUser> oidcUserService;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
        }
    }
    ```

## <a name="build-and-test-your-app"></a>Создание и тестирование приложения

1. Откройте командную строку и перейдите из каталога в папку с файлом *pom.xml*.

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

   ![Сборка приложения][build-application]

1. Скомпилировав и запустив приложение с помощью Maven, перейдите в веб-браузере по адресу http:<span></span>//localhost:8080. Должен появиться запрос на ввод имени пользователя и пароля.

   ![Вход в приложение][application-login]

   > [!NOTE]
   > 
   > Если это первый вход с новой учетной записью пользователя, возможно, вам будет предложено изменить пароль.
   > 
   > ![Изменение пароля][update-password]
   > 

1. Войдя в приложение, вы увидите сообщение контроллера "Hello World".

   ![Успешный вход в приложение][hello-world]

   > [!NOTE]
   > 
   > Пользователи с неавторизованными учетными записями получат сообщение **HTTP 403 — не авторизовано**.
   >

## <a name="summary"></a>Сводка

В этом руководстве вы создали веб-приложение Java с использованием начального приложения Spring Boot для Azure Active Directory, настроили нового клиента Azure AD, зарегистрировали в нем созданное приложение, а затем настроили это приложение для использования аннотаций и классов Spring с целью защиты веб-приложения.

## <a name="see-also"></a>См. также раздел
* Дополнительные сведения о новых параметрах пользовательского интерфейса см. в статье [Новое учебное руководство по регистрации приложений на портале Azure](/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/developer/java/spring-framework)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: /azure/developer/java/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-spring-boot-backend-sample

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png
[create-spring-app-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-02.png
[create-spring-app-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-03.png

[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png
[create-directory-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-05.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03-5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04-5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-06]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-06.png
[create-app-registration-07]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-07.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png
[create-app-registration-11]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-11.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-world]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-world.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png


