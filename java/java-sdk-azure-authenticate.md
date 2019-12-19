---
title: Проверка подлинности с использованием библиотек управления Azure для Java
description: Проверка подлинности с помощью субъекта-службы в библиотеках управления Azure для Java
keywords: Azure, Java, SDK, API, Maven, Gradle, authentication, active directory, service principal
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 10f457e3-578b-4655-8cd1-51339226ee7d
ms.custom: seo-java-september2019
ms.openlocfilehash: 868849e9df89138d943421886961821d4d679db9
ms.sourcegitcommit: 5c65d22b5203b0c17806463d349a6ede93a99fa0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2019
ms.locfileid: "75010539"
---
# <a name="authenticate-with-the-azure-libraries-for-java"></a>Проверка подлинности с использованием библиотек Azure для Java

Узнайте, как выполнять проверку подлинности с использованием библиотек Azure для Java.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-to-services-with-connection-strings"></a>Подключение к службам с помощью строк подключения

Большинство библиотек служб Azure используют строку подключения или ключ безопасности для проверки подлинности. Например, база данных SQL содержит имя пользователя и пароль в строке подключения JDBC:

```java
String url = "jdbc:sqlserver://myazuredb.database.windows.net:1433;" +
        "database=testjavadb;" +
        "user=myazdbuser;" +
        "password=myazdbpass;" +
        "encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";
        Connection conn = DriverManager.getConnection(url);
```

Служба хранилища Azure использует ключ к хранилищу данных для проверки подлинности приложения:

```java
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storageName
        + ";AccountKey=" + storageKey
        + ";EndpointSuffix=core.windows.net";
```

Строки подключения службы используются для проверки подлинности других служб Azure, таких как [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-java-application#UseService), [кэш Redis](https://docs.microsoft.com/azure/redis-cache/cache-java-get-started) и [служебная шина](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-queues). Строки подключения можно получить с помощью портала или интерфейса командной строки Azure.  Также с помощью библиотек управления Azure для Java можно выполнять запросы к ресурсам для создания строк подключения в коде.

Например, этот код использует библиотеки управления для создания строки подключения к учетной записи хранения:

```java
// create a new storage account
StorageAccount storage = azure.storageAccounts().getByResourceGroup("myResourceGroup","myStorageAccount");

// create a storage container to hold the file
List<StorageAccountKey> keys = storage.getKeys();
final String storageConnection = "DefaultEndpointsProtocol=https;"
        + "AccountName=" + storage.name()
        + ";AccountKey=" + keys.get(0).value()
        + ";EndpointSuffix=core.windows.net";
```

Для других библиотек требуется, чтобы приложение работало с [субъектом-службой](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects), который разрешает приложению работать с предоставленными учетными данными. Эта конфигурация похожа на действия проверки подлинности на основе объектов для библиотеки управления, перечисленные ниже.

<a name="mgmt-auth"></a>

##  <a name="authenticate-with-the-azure-management-libraries-for-java"></a>Проверка подлинности с использованием библиотек управления Azure для Java

Проверку подлинности приложения с помощью библиотек управления Azure для Java для создания и управления ресурсами можно выполнить двумя способами.

### <a name="authenticate-with-an-applicationtokencredentials-object"></a>Проверка подлинности с помощью объекта ApplicationTokenCredentials

Создайте экземпляр `ApplicationTokenCredentials`, чтобы предоставить учетные данные субъекта-службы объекту верхнего уровня `Azure` из кода.

```java
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.AzureEnvironment;

// ...

ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(client,
        tenant,
        key,
        AzureEnvironment.AZURE);

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credentials)
        .withDefaultSubscription();
```

`client`, `tenant` и `key` — это те же значения субъекта-службы, которые использовались для [проверки подлинности на основе файлов](#mgmt-file). Значение `AzureEnvironment.AZURE` создает учетные данные для проверки подлинности в общедоступном облаке Azure. Если необходимо получить доступ к другому облаку (например, `AzureEnvironment.AZURE_GERMANY`), измените это значение.

 Значения субъекта-службы можно найти в переменных среды или в хранилище управления секретами, например [Key Vault](/azure/key-vault/key-vault-whatis). Не рекомендуется задавать эти значения как строки открытого текста в коде, чтобы предотвратить случайный доступ к учетным данным из журнала управления версиями.

<a name="mgmt-file"></a>

### <a name="file-based-authentication-preview"></a>Проверка подлинности на основе файлов (предварительная версия)

Самый простой способ реализовать проверку подлинности — создать файл свойств, который содержит учетные данные для [субъекта-службы Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects) в следующем формате:

```text
# sample management library properties file
subscription=########-####-####-####-############
client=########-####-####-####-############
key=XXXXXXXXXXXXXXXX
tenant=########-####-####-####-############
managementURI=https\://management.core.windows.net/
baseURL=https\://management.azure.com/
authURL=https\://login.windows.net/
graphURL=https\://graph.windows.net/
```

- subscription — используйте значение *id* из `az account show` в Azure CLI 2.0.
- client — используйте значение *appId* из выходных данных субъекта-службы, созданного для запуска приложения. Если вы не создали субъект-службу для приложения, [создайте его с помощью Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli).
- key — используйте значение *password* из выходных данных CLI при создании субъекта-службы.
- tenant — используйте значение *tenant* из выходных данных CLI при создании субъекта-службы.

Сохраните этот файл в безопасном расположении в вашей системе, доступном для кода. Задайте переменную среды и укажите полный путь к файлу в оболочке:

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azureauth.properties
```

Создайте точку входа объекта `Azure`, чтобы приступить к работе с библиотеками. Найдите расположение файла свойств с помощью переменной среды.

```java
// pull in the location of the authentication properties file from the environment
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```
