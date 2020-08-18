---
title: Руководство. Развертывание из GitHub в Службе приложений Azure с помощью Jenkins
description: Узнайте, как настроить Jenkins для непрерывной интеграции (CI) и непрерывного развертывания (CD) веб-приложений Java из GitHub в Службе приложений Azure.
keywords: jenkins, azure, devops, app service
ms.topic: tutorial
ms.date: 08/10/2020
ms.openlocfilehash: 3961d413a573d416777f649cef44ceccdecb0b01
ms.sourcegitcommit: e792f681ab66c54e6fd0c7f3cb71816206216d72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075717"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-using-jenkins"></a>Руководство по развертыванию из GitHub в Службе приложений Azure с помощью Jenkins

В рамках этого руководства мы развернем пример веб-приложения Java из GitHub в [Службе приложений Azure на платформе Linux](/azure/app-service/containers/app-service-linux-intro), настроив непрерывную интеграцию (CI) и непрерывное развертывание (CD) в Jenkins. Когда вы обновляете приложение, фиксируя изменения в GitHub, Jenkins автоматически выполняет сборку и повторную публикацию приложения в Службе приложений Azure. Пример приложения, используемый в этом руководстве, разработан на платформе [Spring Boot](https://projects.spring.io/spring-boot/). 

![Общие сведения о развертывании из GitHub в Службе приложений Azure](media/deploy-from-github-to-azure-app-service/azure-continuous-integration-deployment-overview.png)

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * Установите подключаемые модули Jenkins, чтобы выполнять сборку приложений из GitHub, развертывать их в Службе приложений Azure и выполнять другие связанные задачи.
> * Создадите вилку для примера репозитория GitHub, чтобы у вас была рабочая копия.
> * Подключите Jenkins к GitHub.
> * Создадите субъект-службу Azure, чтобы сервер Jenkins мог получить доступ к Azure без использования ваших учетных данных.
> * Добавите субъект-службу в Jenkins.
> * Создадите конвейер Jenkins, который обеспечит сборку и развертывание примера приложения при каждом его обновлении в GitHub.
> * Создадите файлы сборки и развертывания для конвейера Jenkins.
> * Укажете для конвейера Jenkins скрипт сборки и развертывания.
> * Развернете пример приложения в Azure, выполнив сборку вручную.
> * Отправите обновление приложения в GitHub. В результате этого активируется Jenkins для сборки и повторного развертывания в Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Сервер [Jenkins](https://jenkins.io/) с установленными средствами Maven и пакетом Java Development Kit (JDK) на виртуальной машине Azure под управлением Linux.

  Если у вас нет сервера Jenkins, выполните на портале Azure действия, описанные в руководстве по [созданию сервера Jenkins на виртуальной машине Azure под управлением Linux](/azure/jenkins/install-jenkins-solution-template).

* Учетная запись [GitHub](https://github.com), чтобы создать рабочую копию (вилку) для примера веб-приложения Java. 

* [Интерфейс командной строки Azure](/cli/azure/install-azure-cli), который можно запустить из локальной командной строки или [Azure Cloud Shell](/azure/cloud-shell/overview).

## <a name="install-jenkins-plug-ins"></a>Установка подключаемых модулей Jenkins

1. Войдите в веб-консоль Jenkins по этому адресу:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. На главной странице Jenkins выберите **Manage Jenkins** > **Manage Plugins** (Управление Jenkins > Управление подключаемыми модулями).

   ![Управление подключаемыми модулями Jenkins](media/deploy-from-github-to-azure-app-service/manage-plug-ins-for-azure.png)

1. На вкладке **Available** (Доступные) выберите такие подключаемые модули:

   - [Служба приложений Azure](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source);
   - [подключаемый модуль Environment Injector](https://plugins.jenkins.io/envinject) Jenkins;
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials).

   Если эти подключаемые модули не отображаются, перейдите на вкладку **Installed** (Установленные) и убедитесь, что они не установлены.

1. Чтобы установить выбранные подключаемые модули, выберите **Download now and install after restart** (Скачать сейчас и установить после перезагрузки).

1. Когда все будет готово, в меню Jenkins выберите **Manage Jenkins** (Управление Jenkins). Вы вернетесь на страницу управления Jenkins для выполнения следующих шагов.

## <a name="fork-sample-github-repo"></a>Создание вилки для примера репозитория GitHub

1. [Войдите в репозиторий GitHub для примера приложения Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. В правом верхнем углу в GitHub выберите **Fork** (Создать вилку).

   ![Создание вилки для примера репозитория из GitHub](media/deploy-from-github-to-azure-app-service/fork-github-repo.png)

1. Следуя инструкциям на экране, выберите свою учетную запись GitHub и завершите создание вилки.

После этого настройте Jenkins, используя свои учетные данные для GitHub.

## <a name="connect-jenkins-to-github"></a>Подключение Jenkins к GitHub

Чтобы обеспечить в Jenkins отслеживание изменений в GitHub и реагирование на новые фиксации, отправленные в веб-приложение в вилке GitHub, включите [веб-перехватчики GitHub](https://developer.github.com/webhooks/) в Jenkins.

> [!NOTE]
> 
> Выполнив эти действия, вы создадите учетные данные на основе личного маркера доступа, чтобы обеспечить взаимодействие Jenkins и GitHub с помощью ваших имени пользователя и пароля GitHub. 
> Если для вашей учетной записи GitHub используется двухфакторная проверка подлинности, создайте маркер в GitHub и настройте Jenkins на использование этого маркера. 
> Дополнительные сведения см. в документации о [подключаемом модуле GitHub для Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. На странице **Manage Jenkins** (Управление Jenkins) щелкните **Configure System** (Настройка системы). 

   ![Настройка системы в Jenkins](media/deploy-from-github-to-azure-app-service/manage-jenkins-configure-system.png)

1. В разделе **GitHub** укажите сведения о своем сервере GitHub. В списке **Add GitHub Server** (Добавить сервер GitHub) выберите **GitHub Server** (Сервер GitHub). 

   ![Добавление сервера GitHub в Jenkins](media/deploy-from-github-to-azure-app-service/add-GitHub-server.png)

1. Выберите свойство **Manage hooks** (Управление обработчиками), если оно не выбрано. Чтобы указать другие параметры, щелкните **Advanced** (Дополнительно). 

   ![Настройка дополнительных параметров Jenkins для сервера GitHub](media/deploy-from-github-to-azure-app-service/advanced-GitHub-settings.png)

1. В списке **Manage additional GitHub actions** (Управление дополнительными действиями GitHub) выберите **Convert login and password to token** (Преобразовать имя пользователя и пароль в маркер).

   ![Преобразование имени входа и пароля в токен для GitHub](media/deploy-from-github-to-azure-app-service/manage-additional-actions.png)

1. Выберите **From login and password** (Из имени пользователя и пароля), чтобы ввести имя пользователя и пароль для GitHub. Когда все будет готово, нажмите кнопку **Create token credentials** (Создать учетные данные в формате маркера), чтобы создать [личный маркер доступа (PAT) GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Создание PAT для GitHub на основе имени пользователя и пароля](media/deploy-from-github-to-azure-app-service/create-github-token-credentials.png)

1. В разделе **GitHub Server** (Сервер GitHub) в списке **Credentials** (Учетные данные) выберите новый маркер. Убедитесь, что проверка подлинности работает, выбрав **Test connection** (Проверить подключение).

   ![Проверка подключения к серверу GitHub с использованием нового маркера PAT](media/deploy-from-github-to-azure-app-service/check-github-connection.png)

Теперь создайте субъект-службу Azure, который будет использоваться в Jenkins для проверки подлинности и доступа к ресурсам Azure.

## <a name="create-service-principal"></a>Создание субъекта-службы

В одном из следующих разделов вы создадите задание конвейера Jenkins, чтобы выполнить сборку приложения из GitHub и развернуть его в Службе приложений Azure. Чтобы сервер Jenkins мог получить доступ к Azure без ваших учетных данных, воспользуйтесь [субъектом-службой](/active-directory/develop/app-objects-and-service-principals). Если у вас уже есть субъект-служба, можете пропустить этот раздел.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) Azure CLI.

```azurecli-interactive
az ad sp create-for-rbac
```

**Примечания**

- После успешного выполнения `az ad sp create-for-rbac` отображает несколько значений. Значения `name`, `password` и `tenant` используются на следующем шаге.
- По умолчанию субъект-служба создает с ролью **Участник**, которая имеет полные разрешения на чтение учетной записи Azure и записи в нее. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).
- Пароль невозможно извлечь, если он утерян. Следовательно, пароль нужно хранить в надежном месте. Если вы забыли пароль, потребуется [сбросить учетные данные субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).


## <a name="add-service-principal-to-jenkins"></a>Добавление субъекта-службы в Jenkins

1. На главной странице Jenkins выберите **Credentials** > **System** (Учетные данные > Система). 

1. На странице **System** (Система) в разделе **Domain** (Домен) выберите **Global credentials (unrestricted)** (Глобальные учетные данные (без ограничений)).

1. В меню слева выберите **Add Credentials** (Добавить учетные данные).

1. В списке **Kind** (Тип) выберите **Azure Service Principal** (Субъект-служба Azure).

1. Укажите сведения для субъекта-службы и подписки Azure в свойствах, которые описаны в таблице на этом шаге.

   ![Добавление учетных данных субъекта-службы Azure](media/deploy-from-github-to-azure-app-service/add-service-principal-credentials.png)

   | Property (Свойство) | Значение | Описание | 
   |----------|-------|-------------| 
   | **Идентификатор подписки** | <*yourAzureSubscription-ID*> | Значение идентификатора GUID для подписки Azure. <p>**Совет**. Если вы не знаете идентификатор своей подписки Azure, выполните следующую команду Azure CLI из командной строки или Cloud Shell, а затем используйте значение GUID `id`: <p>`az account list` | 
   | **Идентификатор клиента** | <*yourAzureServicePrincipal-ID*> | Значение GUID `appId`, ранее созданное для субъекта-службы Azure. | 
   | **Секрет клиента** | <*yourSecurePassword*> | Значение `password` или секрет, который вы указали для субъекта-службы Azure. | 
   | **Идентификатор клиента** | <*yourAzureActiveDirectoryTenant-ID*> | Значение GUID `tenant` для клиента Azure Active Directory. | 
   | **Идентификатор** | <*yourAzureServicePrincipalName*> | Значение `displayName` для субъекта-службы Azure. | 

1. Чтобы проверить, работает ли субъект-служба, выберите **Verify Service Principal** (Проверить субъект-службу). Закончив, нажмите кнопку **OK**.

Теперь создайте конвейер Jenkins для сборки и развертывания приложения.

## <a name="create-jenkins-pipeline"></a>Создание конвейера Jenkins

В Jenkins создайте задание конвейера для сборки и развертывания приложения.

1. Вернитесь на домашнюю страницу Jenkins и выберите **New Item** (Новый элемент). 

   ![Создание конвейера Jenkins](media/deploy-from-github-to-azure-app-service/jenkins-select-new-item.png)

1. Укажите имя для задания конвейера, например My-Java-Web-App, и выберите **Pipeline** (Конвейер). Нажмите кнопку **OK** внизу.  

   ![Указание имени для задания конвейера Jenkins](media/deploy-from-github-to-azure-app-service/jenkins-select-pipeline.png)

1. Настройте в Jenkins использование субъекта-службы, чтобы сервер Jenkins выполнял развертывание в Azure без использования ваших учетных данных.

   1. На вкладке **General** (Общие) выберите **Prepare an environment for the run** (Подготовка среды для запуска). 

   1. В появившееся поле **Properties Content** (Содержимое свойств) добавьте эти переменные среды с соответствующими значениями. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Подготовка среды для запуска и указание переменных среды](media/deploy-from-github-to-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

Теперь создайте скрипты сборки и развертывания для Jenkins.

## <a name="create-build-and-deployment-files"></a>Создание файлов сборки и развертывания

Создайте файлы, которые Jenkins будет использовать для сборки и развертывания приложения.

1. В папке `src/main/resources/` в вилке GitHub создайте файл конфигурации приложения с именем `web.config`, который содержит следующий XML-код, заменив `$(JAR_FILE_NAME)` на `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. В корневой папке вилки GitHub создайте скрипт сборки и развертывания с именем `Jenkinsfile`, который содержит следующий текст ([исходный код в GitHub см. здесь](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Зафиксируйте файлы `web.config` и `Jenkinsfile` в вилке GitHub и отправьте изменения.

## <a name="point-pipeline-at-script"></a>Указание скрипта для конвейера

Теперь укажите скрипт сборки и развертывания, который должен использоваться в Jenkins.

1. В Jenkins выберите ранее созданное задание конвейера. 

   ![Выбор задания конвейера Jenkins для веб-приложения](media/deploy-from-github-to-azure-app-service/select-pipeline-job.png)

1. В меню слева выберите **Configure** (Настройка).

1. На вкладке **Pipeline** (Конвейер) из списка **Definition** (Определение) выберите **Pipeline script from SCM** (Скрипт конвейера из SCM).

   1. В появившемся поле **SCM** выберите **Git** в качестве системы управления версиями. 

   1. В разделе **Repositories** (Репозитории) для **Repository URL** (URL-адрес репозитория) укажите URL-адрес вилки GitHub, например: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. В поле **учетных данных** выберите личный маркер доступа GitHub, который вы создали ранее.

   1. В поле **Script Path** (Путь к скрипту) добавьте путь к скрипту Jenkinsfile.

   Когда все будет готово, определение конвейера будет выглядеть приблизительно так: 

   ![Указание конвейера Jenkins в скрипте](media/deploy-from-github-to-azure-app-service/set-up-jenkins-github.png)

1. Когда все будет готово, нажмите кнопку **Сохранить**.

Теперь выполните сборку и разверните приложение в Службе приложений Azure. 

## <a name="build-and-deploy-to-azure"></a>Сборка и развертывание в Azure

1. С помощью Azure CLI из командной строки или Azure Cloud Shell создайте [веб-приложение Службы приложений Azure на платформе Linux](/azure/app-service/containers/app-service-linux-intro), где Jenkins развернет веб-приложение после сборки. Обязательно укажите уникальное имя для веб-приложения.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Дополнительные сведения об этих командах Azure CLI см. в соответствующих статьях:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. В Jenkins выберите задание конвейера и щелкните **Build Now** (Начать сборку).

   После завершения сборки Jenkins развернет приложение, которое теперь доступно в Azure по URL-адресу публикации, например: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Просмотр развернутого приложения в Azure](media/deploy-from-github-to-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Отправка изменений и повторное развертывание

1. В веб-браузере перейдите в следующее расположение в вилке GitHub веб-приложения:

   `complete/src/main/java/Hello/Application.java`
   
1. В правом верхнем углу в GitHub выберите **Edit this file** (Изменить этот файл).

1. Внесите приведенные ниже изменения в метод `commandLineRunner()` и зафиксируйте их в ветви `master` репозитория. После этой фиксации в ветви `master` запустится сборка в Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Когда сборка будет завершена и Jenkins выполнит повторное развертывание в Azure, обновите приложение, в котором теперь отображаются обновления.

   ![Просмотр развернутого приложения в Azure](media/deploy-from-github-to-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Устранение неполадок подключаемого модуля Jenkins

Если вы столкнулись с ошибками, которые касаются подключаемых модулей Jenkins, сообщите о проблеме с конкретным компонентом в [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Использование агентов виртуальных машин для непрерывной интеграции с Jenkins](/azure/jenkins/jenkins-azure-vm-agents)
