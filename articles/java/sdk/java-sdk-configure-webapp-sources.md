---
title: Настройка развертываний веб-приложений с использованием Java | Документация Майкрософт
description: Пример кода Java для настройки развертываний Git или FTP в службе приложений Azure с использованием пакета Azure SDK для Java
author: rloutlaw
ms.assetid: 833e9c78-1e50-4c23-a611-f73a2f0c2983
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: c6fcf68e5ac4509744a618fab8b80f09ce24f052
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82105025"
---
# <a name="configure-azure-app-service-deployment-sources-from-your-java-applications"></a>Настройка источников развертывания в службе приложений Azure из приложений Java

В [этом примере](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel) развертывается код для четырех приложений в рамках одного плана [службы приложений Azure](/azure/app-service/). Для каждого приложения используется отдельный источник развертывания.

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps.git
cd app-service-java-configure-deployment-sources-for-web-apps
mvn clean compile exec:java
```

См. [полный пример кода на GitHub](https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps/blob/master/src/main/java/com/microsoft/azure/management/appservice/samples/ManageWebAppSourceControl.java).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-app-service-app-running-apache-tomcat"></a>Создание приложения службы приложений под управлением Apache Tomcat

```java
// create a new Standard app service plan and create a single Java 8/Tomcat 8 app in it
WebApp app1 = azure.webApps().define(app1Name)
             .withNewResourceGroup(rgName)
             .withNewAppServicePlan(planName)
             .withRegion(Region.US_WEST)
             .withPricingTier(AppServicePricingTier.STANDARD_S1)
             .withJavaVersion(JavaVersion.JAVA_8_NEWEST)
             .withWebContainer(WebContainer.TOMCAT_8_0_NEWEST)
             .create();
```

`withJavaVersion()` и `withWebContainer()` настраивают службу приложений для обслуживания HTTP-запросов с использованием Tomcat 8.

## <a name="deploy-a-java-application-using-ftp"></a>Развертывание приложения Java с использованием протокола FTP
```java
// pass the PublishingProfile that contains FTP information to a helper method 
uploadFileToFtp(app1.getPublishingProfile(), "helloworld.war", 
      ManageWebAppSourceControl.class.getResourceAsStream("/helloworld.war"));

// Use the FTP classes in the Apache Commons library to connect to Azure using 
// the information from the PublishingProfile
private static void uploadFileToFtp(PublishingProfile profile, String fileName, InputStream file) throws Exception {
        FTPClient ftpClient = new FTPClient();
        String[] ftpUrlSegments = profile.ftpUrl().split("/", 2);
        String server = ftpUrlSegments[0];
        // Tomcat will deploy WAR files uploaded to this directory.
        String path = "./site/wwwroot/webapps"; 

        // FTP the build WAR to Azure
        ftpClient.connect(server);
        ftpClient.login(profile.ftpUsername(), profile.ftpPassword());
        ftpClient.setFileType(FTP.BINARY_FILE_TYPE);
        ftpClient.changeWorkingDirectory(path);
        ftpClient.storeFile(fileName, file);
        ftpClient.disconnect();
}
```

Этот код передает WAR-файл в каталог `/site/wwwroot/webapps`. Tomcat развертывает WAR-файлы, по умолчанию помещенные в эту папку в службе приложений.

## <a name="deploy-a-java-application-from-a-local-git-repo"></a>Развертывание приложения Java из локального репозитория Git

```java
// get the publishing profile from the App Service webapp
PublishingProfile profile = app2.getPublishingProfile();

// create a new Git repo in the sample directory under src/main/resources 
Git git = Git
    .init()
    .setDirectory(new File(ManageWebAppSourceControl.class.getResource("/azure-samples-appservice-helloworld/").getPath()))
    .call();
git.add().addFilepattern(".").call();
// add the files in the sample app to an initial commit
git.commit().setMessage("Initial commit").call(); 

// push the commit using the Azure Git remote URL and credentials in the publishing profile
PushCommand command = git.push();
command.setRemote(profile.gitUrl()); 
command.setCredentialsProvider(new UsernamePasswordCredentialsProvider(profile.gitUsername(), profile.gitPassword()));
command.setRefSpecs(new RefSpec("master:master")); 
command.setForce(true);
command.call();
```      

Этот код использует библиотеки [JGit](https://eclipse.org/jgit/), чтобы создать репозиторий Git в папке `src/main/resources/azure-samples-appservice-helloworld`. Затем в примере все файлы добавляются в папку для начальной фиксации. Фиксация отправляется в Azure с использованием сведений о развертывании Git из `PublishingProfile` веб-приложения. 

>[!NOTE]
> Макет файлов в репозитории должен точно соответствовать предполагаемому способу их развертывания в каталоге `/site/wwwroot/` в службе приложений Azure.

## <a name="deploy-an-application-from-a-public-git-repo"></a>Развертывание приложения из общедоступного репозитория Git

```java
// deploy a .NET sample app from a public GitHub repo into a new webapp
WebApp app3 = azure.webApps().define(app3Name)
                .withNewResourceGroup(rgName)
                .withExistingAppServicePlan(plan)
                .defineSourceControl()
                .withPublicGitRepository(
                   "https://github.com/Azure-Samples/app-service-web-dotnet-get-started")
                .withBranch("master")
                .attach()
                .create();
 ```

 Среда выполнения службы приложений автоматически собирает и развертывает проект .NET с использованием последней версии кода в ветви `master` репозитория.

## <a name="continuous-deployment-from-a-github-repo"></a>Непрерывное развертывание из репозитория GitHub

```java
// deploy the application whenever you push a new commit or merge a pull request into your master branch
WebApp app4 = azure.webApps()
                    .define(app4Name)
                    .withExistingResourceGroup(rgName)
                    .withExistingAppServicePlan(plan)
                    // Uncomment the following lines to turn on continuous deployment scenario
                    //.defineSourceControl()
                    //    .withContinuouslyIntegratedGitHubRepository("username", "reponame")
                    //    .withBranch("master")
                    //    .withGitHubAccessToken("YOUR GITHUB PERSONAL TOKEN")
                    //    .attach()
                    .create();
```  

`username` и `reponame` — значения, использующиеся в GitHub. [Создайте личный маркер доступа GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) с разрешениями на чтение в репозитории, а затем передайте его в `withGitHubAccessToken`. 


## <a name="sample-explanation"></a>Описание примера

В примере создается первое приложение с помощью Java 8 и Tomcat 8. Оно выполняется в созданном плане службы приложений [Стандартный](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview). Затем код передает WAR-файл по протоколу FTP, используя сведения в объекте `PublishingProfile`, а Tomcat развертывает его.

Второе приложение используется в рамках того же плана, что и первое. Оно также настраивается как приложение Java 8 и Tomcat 8. Библиотеки JGit создают репозиторий Git в папке с распакованным веб-приложением Java в структуре каталогов, которая сопоставляется с данными службы приложений. Новая фиксация добавляет файлы в папке в новый репозиторий Git. Git отправляет фиксацию в Azure с использованием URL-адреса удаленного объекта, а также имени пользователя и пароля, указанных в `PublishingProfile` веб-приложения.

Третье приложение не настраивается для Java и Tomcat. Вместо этого пример .NET развертывается в общедоступном репозитории GitHub непосредственно из источника.

Четвертое приложение развертывает код в главной ветви при каждой отправке изменений или слиянии запроса на вытягивание в главной ветви репозитория GitHub.

| Класс, используемый в примере | Примечания
|-------|-------|
| [WebApp](/java/api/com.microsoft.azure.management.appservice.webapp) | Создается из текучей цепочки `azure.webApps().define()....create()`. Создает веб-приложение службы приложений и любые ресурсы, требующиеся для приложения. Большинство методов обращаются к объекту, чтобы получить сведения о конфигурации. Но методы команд, например `restart()`, изменяют состояние веб-приложения.
| [WebContainer](/java/api/com.microsoft.azure.management.appservice.webcontainer) | Класс со статическими общедоступными полями, которые используются в качестве параметров для `withWebContainer()` при определении WebApp для выполнения в веб-контейнере Java. Содержит варианты для версий Tomcat и Jetty.
| [PublishingProfile](/java/api/com.microsoft.azure.management.appservice.publishingprofile) | Класс, получаемый с помощью объекта WebApp с использованием метода `getPublishingProfile()`. Содержит сведения о развертывании FTP и Git, включая имя пользователя и пароль для развертывания (отличные от данных учетной записи Azure или учетных данных субъекта-службы).
| [AppServicePlan](/java/api/com.microsoft.azure.management.appservice.appserviceplan) | Класс, возвращаемый `azure.appServices().appServicePlans().getByResourceGroup()`. Доступны методы для проверки производительности, уровня и числа веб-приложений, выполняющихся в плане.
| [AppServicePricingTier](/java/api/com.microsoft.azure.management.appservice.PricingTier) | Класс со статическими общедоступными полями, которые представляют уровни службы приложений. Используется для определения встроенного уровня плана при создании приложения с использованием `withPricingTier()` или непосредственно при определении плана с помощью `azure.appServices().appServicePlans().define()`.
| [JavaVersion](/java/api/com.microsoft.azure.management.appservice.javaversion) | Класс со статическими общедоступными полями, которые представляют версии Java, поддерживаемые службой приложений. Используется с `withJavaVersion()` во время выполнения цепочки `define()...create()` при создании нового веб-приложения.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [next-steps](includes/java-next-steps.md)]