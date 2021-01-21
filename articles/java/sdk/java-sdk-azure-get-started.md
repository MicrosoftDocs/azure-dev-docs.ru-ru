---
title: Начало работы с пакетом Azure SDK для Java
description: Сведения о создании, подключении и использовании облачных ресурсов Azure в приложениях Java.
keywords: Azure, Java, SDK, API, authenticate, get-started
author: bmitchell287
ms.author: brendm
ms.date: 11/20/2020
ms.topic: article
ms.service: multiple
ms.assetid: b1e10b79-f75e-4605-aecd-eed64873e2d3
ms.custom: seo-java-august2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: f96b8f122967c1b742fb97a0ce5d096885904f59
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561840"
---
# <a name="get-started-with-cloud-development-using-java-on-azure"></a>Начало работы с разработкой в облаке с помощью Java в Azure

В этой статье показано, как настроить среду разработки в Azure для разработки на Java. Вы создадите ресурсы Azure и подключитесь к ним для выполнения таких базовых задач, как передача файла или развертывание веб-приложения. Когда все будет готово, вы сможете использовать службы Azure в приложениях Java.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure. Если у вас ее нет, [получите бесплатную пробную версию](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) или [Azure CLI 2.0](/cli/azure/install-az-cli2).
- [Java 8](../fundamentals/java-jdk-long-term-support.md) в составе Azure Cloud Shell.
- [Maven 3](https://maven.apache.org/download.cgi) в составе Azure Cloud Shell.

## <a name="set-up-authentication"></a>Настройка проверки подлинности

Чтобы выполнить пример кода из этого руководства, предоставьте приложению Java в вашей подписке Azure разрешения на чтение и создание. Создайте субъект-службу и настройте приложение для выполнения со связанными учетными данными. Субъект-служба помогает создать неинтерактивную учетную запись, связанную с вашим идентификатором. Этой учетной записи предоставляются только разрешения, необходимые для запуска приложения.

[Создайте субъект-службу с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli) и запишите выходные данные:

```azurecli-interactive
az ad sp create-for-rbac --name AzureJavaTest
```

Вы получите ответ в следующем формате:

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureJavaTest",
  "name": "http://AzureJavaTest",
  "password": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
}
```

Теперь настройте переменные среды:

- `AZURE_SUBSCRIPTION_ID`: используйте значение *id* из выходных данных `az account show` в Azure CLI 2.0.
- `AZURE_CLIENT_ID`: используйте значение *appId* из выходных данных субъекта-службы.
- `AZURE_CLIENT_SECRET`: используйте значение *password* из выходных данных субъекта-службы.
- `AZURE_TENANT_ID`: используйте значение *tenant* из выходных данных субъекта-службы.

Сведения о дополнительных вариантах проверки подлинности см. в документации по [удостоверению Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-java).

## <a name="tooling"></a>Инструментарий

### <a name="create-a-new-maven-project"></a>Создание нового проекта Maven

> [!NOTE]
> В рамках этой статьи для сборки и запуска примера кода используется средство сборки Maven. Для работы с библиотеками Azure для Java есть и другие средства сборки, например Gradle.

Создайте проект Maven из командной строки в новом каталоге на локальном компьютере.

```shell
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=AzureApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

На этом этапе создается базовый проект Maven в папке `testAzureApp`. Добавьте следующие записи в проект `pom.xml`, чтобы импортировать библиотеки, используемые в примере кода в этом руководстве.

```XML
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.8.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.1.jre8</version>
</dependency>
```

Добавьте запись `build` в элемент верхнего уровня `project`, чтобы использовать [maven-exec-plugin](https://www.mojohaus.org/exec-maven-plugin/) для выполнения примеров.

```XML
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.AzureApp</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
 ```

### <a name="install-the-azure-toolkit-for-intellij"></a>Установка Azure Toolkit for IntelliJ

[Набор средств Azure](../toolkit-for-intellij/index.yml) нужен, только если вы намерены развертывать веб-приложения или API программными средствами. Сейчас он не используется для других видов разработки. Ниже приведены инструкции по установке. Краткое руководство см. в документации по [Azure Toolkit for IntelliJ](../toolkit-for-intellij/create-hello-world-web-app.md).

1. В меню **Файл** выберите элемент **Параметры**.
1. Выберите элемент **Обзор репозиториев**, выполните поиск по строке **Azure** и установите **Azure Toolkit for IntelliJ**.
1. Перезапустите Intellij.

### <a name="install-the-azure-toolkit-for-eclipse"></a>Установка набора средств Azure для Eclipse

[Набор средств Azure](../toolkit-for-eclipse/index.yml) нужен, только если вы намерены развертывать веб-приложения или API программными средствами. Сейчас он не используется для других видов разработки. Ниже приведены инструкции по установке. Краткое руководство см. в документации по [Azure Toolkit for Eclipse](../toolkit-for-eclipse/create-hello-world-web-app.md).

1. В меню **Help** (Справка) выберите пункт **Install New Software** (Установка нового программного обеспечения).
1. В поле **Work with** (Совместная работа с) выберите `http://dl.microsoft.com/eclipse/` и нажмите **ВВОД**.
1. Установите флажок **Azure toolkit for Java** (Набор средств Azure для Java). Снимите флажок **Contact all update sites during install to find required software** (Опрашивать при установке все сайты обновления для поиска необходимого программного обеспечения). Выберите **Далее**.

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

Создайте файл с именем `AzureApp.java` в каталоге проекта `src/main/java/com/fabrikam` и вставьте в него приведенный ниже блок кода. Обновите переменные `userName` и `sshKey`, указав реальные значения для своего компьютера. Этот код позволяет создать виртуальную машину Linux с именем `testLinuxVM` в группе ресурсов `sampleResourceGroup`, которая выполняется в регионе Azure "Восточная часть США".

```java
package com.fabrikam;

import com.azure.core.credential.TokenCredential;
import com.azure.core.http.policy.HttpLogDetailLevel;
import com.azure.core.management.AzureEnvironment;
import com.azure.core.management.Region;
import com.azure.core.management.profile.AzureProfile;
import com.azure.identity.AzureAuthorityHosts;
import com.azure.identity.EnvironmentCredentialBuilder;
import com.azure.resourcemanager.AzureResourceManager;
import com.azure.resourcemanager.compute.models.KnownLinuxVirtualMachineImage;
import com.azure.resourcemanager.compute.models.VirtualMachine;
import com.azure.resourcemanager.compute.models.VirtualMachineSizeTypes;

public class AzureApp {

    public static void main(String[] args) {

        final String userName = "YOUR_VM_USERNAME";
        final String sshKey = "YOUR_PUBLIC_SSH_KEY";

        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            // Create an Ubuntu virtual machine in a new resource group.
            VirtualMachine linuxVM = azureResourceManager.virtualMachines().define("testLinuxVM")
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleVmResourceGroup")
                    .withNewPrimaryNetwork("10.0.0.0/24")
                    .withPrimaryPrivateIPAddressDynamic()
                    .withoutPrimaryPublicIPAddress()
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withUnmanagedDisks()
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
}
```

Запустите этот пример из командной строки.

```shell
mvn compile exec:java
```

В консоли будут появляться запросы и ответы REST, по мере того как пакет SDK выполняет базовые вызовы к REST API Azure для настройки виртуальной машины и ее ресурсов. Когда эта программа завершит работу, проверьте наличие виртуальной машины в подписке с помощью Azure CLI 2.0.

```azurecli-interactive
az vm list --resource-group sampleVmResourceGroup
```

Убедившись, что код выполнил свою работу, с помощью CLI удалите виртуальную машину и ее ресурсы.

```azurecli-interactive
az group delete --name sampleVmResourceGroup
```

## <a name="deploy-a-web-app-from-a-github-repo"></a>Развертывание веб-приложения из репозитория GitHub

Замените в `AzureApp.java` метод main на следующий код. Перед выполнением кода присвойте переменной `appName` уникальное значение. Этот код развертывает веб-приложение из ветви `master` в общедоступном репозитории GitHub в новое [веб-приложение службы приложений Azure](/azure/app-service-web/app-service-web-overview) ценовой категории "Бесплатный".

```java
    public static void main(String[] args) {
        try {

            final String appName = "YOUR_APP_NAME";

            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);
            
            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            WebApp app = azureResourceManager.webApps().define(appName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleWebResourceGroup")
                    .withNewWindowsPlan(PricingTier.FREE_F1)
                    .defineSourceControl()
                    .withPublicGitRepository(
                            "https://github.com/Azure-Samples/app-service-web-java-get-started")
                    .withBranch("master")
                    .attach()
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Выполните этот код перед началом работы с Maven.

```shell
mvn clean compile exec:java
```

С помощью CLI откройте в браузере адрес приложения.

```azurecli-interactive
az appservice web browse --resource-group sampleWebResourceGroup --name YOUR_APP_NAME
```

Завершив развертывание, удалите веб-приложение и план из подписки.

```azurecli-interactive
az group delete --name sampleWebResourceGroup
```

## <a name="connect-to-an-azure-sql-database"></a>Подключение к базе данных SQL Azure

Замените в `AzureApp.java` существующий код метода main приведенным ниже кодом. Укажите реальные значения для переменных.
Этот код позволяет создать базу данных SQL с правилом брандмауэра, которое разрешает удаленный доступ. Затем этот код подключается к ней с использованием драйвера JDBC Базы данных SQL.

```java
    public static void main(String args[])
    {
        // Create the db using the management libraries.
        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile);

            final String adminUser = "YOUR_USERNAME_HERE";
            final String sqlServerName = "YOUR_SERVER_NAME_HERE";
            final String sqlDbName = "YOUR_DB_NAME_HERE";
            final String dbPassword = "YOUR_PASSWORD_HERE";
            final String firewallRuleName = "YOUR_RULE_NAME_HERE";

            SqlServer sampleSQLServer = azureResourceManager.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleSqlResourceGroup")
                    .withAdministratorLogin(adminUser)
                    .withAdministratorPassword(dbPassword)
                    .defineFirewallRule(firewallRuleName)
                        .withIpAddressRange("0.0.0.0","255.255.255.255")
                        .attach()
                    .create();

            SqlDatabase sampleSQLDb = sampleSQLServer.databases().define(sqlDbName).create();

            // Assemble the connection string to the database.
            final String domain = sampleSQLServer.fullyQualifiedDomainName();
            String url = "jdbc:sqlserver://"+ domain + ":1433;" +
                    "database=" + sqlDbName +";" +
                    "user=" + adminUser+ "@" + sqlServerName + ";" +
                    "password=" + dbPassword + ";" +
                    "encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";

            // Connect to the database, create a table, and insert an entry into it.
            Connection conn = DriverManager.getConnection(url);

            String createTable = "CREATE TABLE CLOUD ( name varchar(255), code int);";
            String insertValues = "INSERT INTO CLOUD (name, code ) VALUES ('Azure', 1);";
            String selectValues = "SELECT * FROM CLOUD";
            Statement createStatement = conn.createStatement();
            createStatement.execute(createTable);
            Statement insertStatement = conn.createStatement();
            insertStatement.execute(insertValues);
            Statement selectStatement = conn.createStatement();
            ResultSet rst = selectStatement.executeQuery(selectValues);

            while (rst.next()) {
                System.out.println(rst.getString(1) + " "
                        + rst.getString(2));
            }


        } catch (Exception e) {
            System.out.println(e.getMessage());
            System.out.println(e.getStackTrace().toString());
        }
    }
```

Запустите этот пример из командной строки.

```shell
mvn clean compile exec:java
```

Теперь удалите ресурсы с помощью CLI.

```azurecli-interactive
az group delete --name sampleSqlResourceGroup
```

## <a name="write-a-blob-into-a-new-storage-account"></a>Запись большого двоичного объекта в новую учетную запись хранения

Замените в `AzureApp.java` существующий код метода main приведенным ниже кодом. Этот код позволяет [создать учетную запись хранения Azure](/azure/storage/common/storage-introduction). Затем с помощью библиотек службы хранилища Azure для Java в облаке создается текстовый файл.

```java
    public static void main(String[] args) {

        try {
            TokenCredential tokenCredential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(tokenCredential, profile)
                    .withDefaultSubscription();

            // Create a new storage account.
            String storageAccountName = "YOUR_STORAGE_ACCOUNT_NAME_HERE";
            StorageAccount storage = azureResourceManager.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleStorageResourceGroup")
                    .create();

            // Create a storage container to hold the file.
            List<StorageAccountKey> keys = storage.getKeys();
            PublicEndpoints endpoints = storage.endPoints();
            String accountName = storage.name();
            String accountKey = keys.get(0).value();
            String endpoint = endpoints.primary().blob();

            StorageSharedKeyCredential credential = new StorageSharedKeyCredential(accountName, accountKey);

            BlobServiceClient storageClient =new BlobServiceClientBuilder()
                    .endpoint(endpoint)
                    .credential(credential)
                    .buildClient();

            // Container name must be lowercase.
            BlobContainerClient blobContainerClient = storageClient.getBlobContainerClient("helloazure");
            blobContainerClient.create();

            // Make the container public.
            blobContainerClient.setAccessPolicy(PublicAccessType.CONTAINER, null);

            // Write a blob to the container.
            String fileName = "helloazure.txt";
            String textNew = "Hello Azure";

            BlobClient blobClient = blobContainerClient.getBlobClient(fileName);
            InputStream is = new ByteArrayInputStream(textNew.getBytes());
            blobClient.upload(is, textNew.length());

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Запустите этот пример из командной строки.

```shell
mvn clean compile exec:java
```

Перейти к файлу `helloazure.txt` в вашей учетной записи хранения можно с помощью портала Azure или [обозревателя хранилищ Azure](/azure/vs-azure-tools-storage-explorer-blobs).

Удалите учетную запись хранилища с помощью CLI.

```azurecli-interactive
az group delete --name sampleStorageResourceGroup
```

## <a name="explore-more-samples"></a>Другие примеры

Дополнительные сведения о том, как с применением библиотеки управления Azure для Java управлять ресурсами и автоматизировать задачи, см. в наших примерах кода для [виртуальных машин](java-sdk-azure-virtual-machine-samples.md), [веб-приложений](java-sdk-azure-web-apps-samples.md) и [Базы данных SQL](java-sdk-azure-sql-database-samples.md).

## <a name="reference-and-release-notes"></a>Ссылки и заметки о выпуске

[Справочник](/java/api) по всем пакетам.

## <a name="get-help-and-give-feedback"></a>Справка и отзывы

Задавайте вопросы сообществу на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/azure+java). Сообщайте об ошибках и нерешенных проблемах с библиотеками Azure для Java на странице [проектов GitHub](https://github.com/Azure/azure-sdk-for-java).