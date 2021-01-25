---
title: Как использовать приложение Spring Boot Starter с Azure Active Directory
description: Сведения о настройке приложения Spring Initializr с помощью начального приложения Azure Active Directory.
services: active-directory
documentationcenter: java
ms.date: 10/14/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 3f4070fa18c9d5d5db7df5d877b490ff0103b0b2
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561461"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Руководство по Защита приложения Java с использованием начального приложения Spring Boot для Azure Active Directory

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
1. Добавьте **зависимости** для **Spring Web**, **Azure Active Directory** и **OAuth2 Client**.
1. Внизу страницы нажмите кнопку **СОЗДАТЬ**.
   
   >[!div class="mx-imgBorder"]
   >![Присвоение имен группе и артефакту, выбор зависимостей][create-spring-app-01]


1. При появлении запроса скачайте проект на локальный компьютер.

## <a name="create-azure-active-directory-instance"></a>Создание экземпляра Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Создание экземпляра Active Directory

1. Войдите на сайт <https://portal.azure.com>.

1. Щелкните **Создать ресурс**, а затем выберите **Удостоверение** и **Azure Active Directory**.
   
   >[!div class="mx-imgBorder"]
   >![Создание экземпляра Azure Active Directory — шаг 1][create-directory-00]

   >[!div class="mx-imgBorder"]
   >![Создание экземпляра Azure Active Directory — шаг 2][create-directory-01]

1. Укажите **имя организации** и **первоначальное доменное имя**. Скопируйте полный URL-адрес вашего каталога. Он вам понадобится, чтобы добавить учетные записи пользователей, как описывается далее в этом руководстве.
 (Например, `azuresampledirectory.onmicrosoft.com`.) 

    Скопируйте полный URL-адрес вашего каталога. Он вам понадобится, чтобы добавить учетные записи пользователей, как описывается далее в этом руководстве. (Например, azuresampledirectory.onmicrosoft.com.)

    По завершении нажмите кнопку **Создать**. Создание ресурса займет несколько минут.
   
   >[!div class="mx-imgBorder"]
   >![Указание имен Azure Active Directory][create-directory-02]

1. По завершении щелкните ссылку, чтобы получить доступ к новому каталогу.
   
   >[!div class="mx-imgBorder"]
   >![Выбор учетной записи Azure][create-directory-03]

1. Скопируйте **идентификатор клиента**. Это значение понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве.
   
   >[!div class="mx-imgBorder"]
   >![Копирование идентификатора клиента][create-directory-04]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Регистрация приложения для приложения Spring Boot

1. В меню на портале выберите **Регистрация приложений**, а затем — **Зарегистрировать приложение**.
   
   >[!div class="mx-imgBorder"]
   >![Регистрация приложения][create-app-registration-01]

1. Выберите свое приложение и щелкните **Зарегистрировать**.
   
   >[!div class="mx-imgBorder"]
   >![Создание регистрации приложения][create-app-registration-02]

1. Когда появится страница регистрации приложения, скопируйте **идентификатор приложения** и **идентификатор клиента**. Эти значения понадобятся при настройке файла *application.properties*, как описывается далее в этом руководстве.
   
   >[!div class="mx-imgBorder"]
   >![Копирование регистрационных ключей приложения][create-app-registration-03]

1. В области навигации слева щелкните **Сертификаты и секреты**.  Затем выберите **Создать секрет клиента**.
   
   >[!div class="mx-imgBorder"]
   >![Создание ключей зарегистрированного приложения][create-app-registration-03-5]

1. Добавьте **описание** и выберите нужное значение в списке **Срок действия**.  Нажмите кнопку **Добавить**. Значение ключа заполнится автоматически.
   
   >[!div class="mx-imgBorder"]
   >![Указание параметров зарегистрированного приложения][create-app-registration-04]

1. Скопируйте и сохраните секрет ключа. Это значение понадобится при настройке файла *application.properties*, как описывается далее в этом руководстве. (Вы не сможете получить это значение позже.)
   
   >[!div class="mx-imgBorder"]
   >![Копирование значения регистрационного ключа приложения][create-app-registration-04-5]

1. Щелкните **Разрешения API** в области навигации слева. 

1. Щелкните элемент **Microsoft Graph** и установите флажки **Доступ к каталогу, аналогичный доступу вошедшего пользователя** и **Вход в систему и чтение профиля пользователя**. Щелкните **Предоставить разрешения...** и выберите **Да** при появлении запроса.
   
   >[!div class="mx-imgBorder"]
   >![Добавление разрешений на доступ][create-app-registration-08]
   
1. Щелкните **Предоставить согласие администратора для Azure Sample** и нажмите кнопку **Да**.
   
   >[!div class="mx-imgBorder"]
   >![Предоставление разрешений на доступ][create-app-registration-05]

1. На главной странице регистрации приложения выберите **Проверка подлинности** и щелкните **Добавить платформу**.  Затем выберите **Веб-приложения**.
   
   >[!div class="mx-imgBorder"]
   >![Изменение URL-адресов ответа][create-app-registration-09]

1. Введите *http://localhost:8080/login/oauth2/code/azure* в качестве нового **URI перенаправления**, а затем щелкните **Настроить**.
   
   >[!div class="mx-imgBorder"]
   >![Добавление нового URL-адреса ответа][create-app-registration-10]

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Добавление учетной записи пользователя в каталог и в группу

1. На странице **Обзор** своего экземпляра службы Active Directory щелкните **Пользователи** и **Новый пользователь**.
   
   >[!div class="mx-imgBorder"]
   >![Добавление новой учетной записи пользователя][create-user-01]

1. Когда появится область **Пользователь**, введите значения в поля **Имя пользователя** и **Имя**.  Щелкните **Создать**.
   
   >[!div class="mx-imgBorder"]
   >![Ввод сведений об учетной записи пользователя][create-user-02]

   > [!NOTE]
   > При вводе имени пользователя укажите URL-адрес каталога, ранее скопированный в рамках этого руководства. Например:
   >
   > `test-user@azuresampledirectory.onmicrosoft.com`

1. На странице **Обзор** своего экземпляра службы Active Directory выберите **Группы** и **Создать группу**, чтобы создать группу, которая будет использоваться для авторизации в приложении.

1. Выберите **Нет выбранных участников**. (В этом руководстве показано, как создать группу с именем *group1*.)  Найдите пользователя, созданного на предыдущем шаге.  Чтобы добавить пользователя в группу, нажмите кнопку **Выбрать**.  Затем нажмите кнопку **Создать**, чтобы создать группу.

   >[!div class="mx-imgBorder"]
   >![Выбор пользователя для группы][create-user-03]

1. Вернитесь в область **Пользователи**, выберите тестового пользователя и щелкните **Сброс пароля**, а затем скопируйте пароль. Он вам понадобится при входе в приложение, как описывается далее в этом учебнике.

   >[!div class="mx-imgBorder"]
   >![Отображение пароля][create-user-04]

## <a name="configure-and-compile-your-app"></a>Настройка и компиляция приложения

1. Распакуйте архив с файлами проекта, который вы создали и скачали в каталог ранее в рамках этого руководства.

1. В папке *src/main/resources* проекта откройте файл *application.properties* в текстовом редакторе.

1. Задайте параметры для регистрации вашего приложения, используя созданные ранее значения. Например:

   ```properties
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222
   # Specifies your App Registration's Application ID:
   azure.activedirectory.client-id=11111111-1111-1111-1111-1111111111111111
   # Specifies your App Registration's secret key:
   azure.activedirectory.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==
   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.user-group.allowed-groups=group1
   ```

   Где:

   | Параметр | Описание |
   |---|---|
   | `azure.activedirectory.tenant-id` | Содержит **идентификатор каталога** Active Directory, который вы скопировали ранее. |
   | `azure.activedirectory.client-id` | Содержит **идентификатор приложения**, полученный после регистрации приложения. |
   | `azure.activedirectory.client-secret` | Содержит **значение** ключа, полученное после регистрации приложения. |
   | `azure.activedirectory.user-group.allowed-groups` | Содержит список групп Active Directory, используемых для авторизации. |

   > [!NOTE]
   > Полный список значений, доступных в файле *application.properties*, см. в примере [Azure Active Directory Spring Boot][AAD Spring Boot Sample] на сайте GitHub.

1. Сохраните и закройте файл *application.properties*.

1. Создайте папку с именем *controller* в папке с исходным кодом Java для приложения, например: *src/main/java/com/wingtiptoys/security/controller*.

1. Создайте файл Java с именем *HelloController.java* в папке *controller* и откройте его в текстовом редакторе.

1. Вставьте следующий код, а затем сохраните и закройте файл:

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.ResponseBody;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.security.access.prepost.PreAuthorize;
   
   @RestController
   public class HelloController {
   
      @GetMapping("group1")
      @ResponseBody
      @PreAuthorize("hasRole('ROLE_group1')")
      public String group1() {
         return "Hello Group 1 Users!";
      }
    
      @GetMapping("group2")
      @ResponseBody
      @PreAuthorize("hasRole('ROLE_group2')")
      public String group2() {
         return "Hello Group 2 Users!";
      }
   }
   ```

   > [!NOTE]
   > Имя группы, указываемое для метода `@PreAuthorize("hasRole('')")`, должно содержать одну из групп, указанных в поле `azure.activedirectory.user-group.allowed-groups` в файле *application.properties*.
   >
   > Для разных сопоставлений запросов можно указывать разные параметры авторизации, например:
   >
   > ``` java
   > public class HelloController {
   >
   >    @PreAuthorize("hasRole('ROLE_Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('ROLE_group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('ROLE_group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```

1. Откройте класс приложения в текстовом редакторе.

1. Добавьте `@EnableWebSecurity` и `@EnableGlobalMethodSecurity(prePostEnabled = true)` в класс приложения, как показано в следующем примере, а затем сохраните и закройте файл:

    ```java
    package com.wingtiptoys;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    
    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    @SpringBootApplication
    public class SpringBootSampleActiveDirectoryApplication {   
        public static void main(String[] args) {
            SpringApplication.run(SpringBootSampleActiveDirectoryApplication.class, args);
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
   
   >[!div class="mx-imgBorder"]
   >![Сборка приложения][build-application]

1. Скомпилировав и запустив приложение с помощью Maven, перейдите в веб-браузере по адресу <http://localhost:8080/group1> и введите имя пользователя и пароль.
   
   >[!div class="mx-imgBorder"]
   ![Вход в приложение][application-login]

   > [!NOTE]
   > Если это первый вход с новой учетной записью пользователя, возможно, вам будет предложено изменить пароль.

   >[!div class="mx-imgBorder"]
   >![Изменение пароля][update-password]

1. Выполнив вход, вы увидите пример текста Hello Group 1 Users! из контроллера.

   >[!div class="mx-imgBorder"]
   >![Authorized_group1][hello-group1]

   > [!NOTE]
   > Пользователи с неавторизованными учетными записями получат сообщение **HTTP 403 — не авторизовано**.

   >[!div class="mx-imgBorder"]
   >![UnAuthorized_group2][Unauthorized-group2]
## <a name="summary"></a>Сводка

В этом руководстве вы создали веб-приложение Java с использованием начального приложения Spring Boot для Azure Active Directory, настроили нового клиента Azure AD, зарегистрировали в нем созданное приложение, а затем настроили это приложение для использования аннотаций и классов Spring с целью защиты веб-приложения.

## <a name="see-also"></a>См. также раздел
* Дополнительные сведения о новых параметрах пользовательского интерфейса см. в статье [Новое учебное руководство по регистрации приложений на портале Azure](/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Spring и Azure см. в центре документации об использовании Spring в Azure.

   >[!div class="nextstepaction"]
   >[Spring в Azure](./index.yml)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: ../index.yml
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png

[create-directory-00]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-00.png
[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03-5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04-5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-group1]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-group1.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png
[Unauthorized-group2]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/unauthorized-group2.png
