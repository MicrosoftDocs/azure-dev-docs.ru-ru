---
title: Как использовать приложение Spring Boot Starter с Azure Active Directory
description: Сведения о настройке приложения Spring Initializr с помощью начального приложения Azure Active Directory.
services: active-directory
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 72916633d4b0df05957b2289ad31559a0c9319bc
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68283365"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Руководство по Защита приложения Java с использованием начального приложения Spring Boot для Azure Active Directory

## <a name="overview"></a>Обзор

В этой статье описывается, как с помощью **[Spring Initializr]** создать начальное приложение Java Spring Boot для Azure Active Directory (Azure AD).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание приложения Java с помощью Spring Initializr;
> * настройка Azure Active Directory;
> * защита приложения с помощью классов и аннотаций Spring Boot;
> * сборка и тестирование приложения Java.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Поддерживаемая версия Java Development Kit (JDK). Дополнительные сведения о версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) версии 3.0 или более поздней.

## <a name="create-an-app-using-spring-initializr"></a>Создание приложения с помощью Spring Initializr

1. Перейдите по адресу <https://start.spring.io/>.

1. Укажите, что необходимо создать проект **Maven** с помощью **Java**, введите имя **группы** и **артефакта** вашего приложения, а затем щелкните ссылку, чтобы **перейти к полной версии** Spring Initializr.

   ![Указание имен группы и артефакта][create-spring-app-01]

1. Прокрутите страницу вниз до раздела **Core** (Основные параметры) и установите флажок **Security** (Безопасность). В разделе **Web** (Веб-приложение) установите флажок **Web** (Веб-приложение). Затем прокрутите страницу вниз до раздела **Azure** и установите флажок **Azure Active Directory**.

   ![Выбор начальных компонентов Security, Web и Azure Active Directory][create-spring-app-02]

1. Прокрутите страницу вверх или вниз до конца и нажмите кнопку **Generate Project** (Создать проект).

   ![Создание проекта Spring Boot][create-spring-app-03]

1. При появлении запроса скачайте проект на локальный компьютер.

## <a name="create-azure-active-directory-instance"></a>Создание экземпляра Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Создание экземпляра Active Directory

1. Войдите на сайт <https://portal.azure.com>.

1. Щелкните **Создать ресурс**, а затем выберите **Удостоверение** и **Azure Active Directory**.

   ![Создание экземпляра Azure Active Directory][create-directory-01]

1. Укажите **имя организации** и **первоначальное доменное имя**. Скопируйте полный URL-адрес вашего каталога. Он вам понадобится, чтобы добавить учетные записи пользователей, как описывается далее в этом руководстве. (Например, `wingtiptoysdirectory.onmicrosoft.com`.) По завершении нажмите кнопку **Создать**.

   ![Указание имен Azure Active Directory][create-directory-02]

1. Выберите имя учетной записи в правом верхнем углу панели инструментов на портале Azure и щелкните **Переключение каталога**.

   ![Выбор учетной записи Azure][create-directory-03]

1. Выберите новый экземпляр Azure Active Directory в раскрывающемся меню.

   ![Выбор экземпляра Azure Active Directory][create-directory-04]

1. В меню портала выберите **Azure Active Directory**, щелкните **Свойства** и скопируйте значение параметра **Идентификатор каталога**. Оно вам понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве.

   ![Копирование идентификатора каталога Azure Active Directory][create-directory-05]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Регистрация приложения для приложения Spring Boot

1. В меню портала выберите **Azure Active Directory**, затем выберите **Регистрация приложений**, после чего щелкните **Регистрация нового приложения**.

   ![Регистрация приложения][create-app-registration-01]

2. Укажите **имя** приложения, используйте http://localhost:8080 в качестве **URL-адреса для входа** и щелкните **Создать**.

   ![Создание регистрации приложения][create-app-registration-02]

4. Когда появится страница для регистрации приложения, скопируйте **идентификатор приложения**. Он вам понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве. Щелкните **Параметры**, а затем выберите **Ключи**.

   ![Создание ключей зарегистрированного приложения][create-app-registration-03]

5. Добавьте **описание** и укажите **длительность** использования нового ключа, а затем щелкните **Сохранить**. Значение ключа будет указано автоматически, когда вы щелкнете значок **Сохранить**. Скопируйте это значение, чтобы использовать его при настройке файла *application.properties*, как описывается далее в этом руководстве. (Вы не сможете получить это значение позже.)

   ![Указание параметров зарегистрированного приложения][create-app-registration-04]

6. На главной странице зарегистрированного приложения последовательно щелкните **Параметры** и **Требуемые разрешения**.

   ![Добавление требуемых разрешений для приложения][create-app-registration-05]

7. Щелкните **Azure Active Directory**.

   ![Выбор Azure Active Directory][create-app-registration-06]

8. Установите флажки рядом с пунктами **Доступ к каталогу как пользователь, выполнивший вход** и **Вход в систему и чтение профиля пользователя**, а затем щелкните **Сохранить**.

   ![Включение разрешений на доступ][create-app-registration-07]

9. На странице **Требуемые разрешения** щелкните **Предоставить разрешения** и **Да**.

   ![Предоставление разрешений на доступ][create-app-registration-08]

10. На главной странице зарегистрированного приложения последовательно щелкните **Параметры** и **URL-адреса ответа**.

    ![Изменение URL-адресов ответа][create-app-registration-09]

11. Введите <http://localhost:8080/login/oauth2/code/azure> в качестве нового URL-адреса ответа, а затем нажмите кнопку **Сохранить**.

    ![Добавление нового URL-адреса ответа][create-app-registration-10]

12. На главной странице регистрации приложения щелкните **Манифест**, затем установите для параметра `oauth2AllowImplicitFlow` значение `true` и нажмите кнопку **Сохранить**.

    ![Настройка манифеста приложения][create-app-registration-11]

    > [!NOTE]
    > 
    > См. подробнее о параметре `oauth2AllowImplicitFlow` и других параметрах приложения в статье [Манифест приложения Azure Active Directory][AAD app manifest]. 
    >

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Добавление учетной записи пользователя в каталог и в группу

1. На странице **Обзор** своего экземпляра службы Active Directory щелкните **Все пользователи**, а затем — **Новый пользователь**.

   ![Добавление новой учетной записи пользователя][create-user-01]

1. Когда появится панель **Пользователь**, введите значения в поля **Имя** и **Имя пользователя**.

   ![Ввод сведений об учетной записи пользователя][create-user-02]

   > [!NOTE]
   > 
   > При вводе имени пользователя укажите URL-адрес каталога, ранее скопированный в рамках этого руководства. Например:
   >
   > `wingtipuser@wingtiptoysdirectory.onmicrosoft.com`
   > 

1. Щелкните **Группы**, затем выберите группы, которые будут использоваться для авторизации в приложении, и нажмите кнопку **Выбрать**. (В рамках этого руководства добавьте учетную запись в группу _Пользователи_.)

   ![Выбор групп пользователей][create-user-03]

1. Установите флажок **Показать пароль** и скопируйте пароль. Он вам понадобится при входе в приложение, как описывается далее в этом руководстве. После того как вы скопировали пароль, щелкните **Создать**, чтобы добавить новую учетную запись пользователя в свой каталог.

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
   Описание

   | Параметр | ОПИСАНИЕ |
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

1. Скомпилировав и запустив приложение с помощью Maven, перейдите в веб-браузере по адресу <http://localhost:8080> и введите имя пользователя и пароль.

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

> [!div class="nextstepaction"]
> [Spring в Azure](/azure/java/spring-framework)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: /azure/java/
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
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
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
