---
title: Проверка подлинности приложений Java, размещенных в Azure
description: Обзор концепций SDK Azure для Java, связанных с проверкой подлинности приложений, размещенных в Azure
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: e9097a432abf5957c9983ec2846bfb18ba581db4
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528683"
---
# <a name="authenticate-azure-hosted-java-applications"></a>Проверка подлинности приложений Java, размещенных в Azure

В этой статье рассматривается, как библиотека удостоверений Azure поддерживает проверку подлинности маркеров Azure Active Directory для приложений, размещенных в Azure. Эта поддержка возможна благодаря набору реализаций `TokenCredential`, описанных ниже.

В этой статье рассматриваются следующие темы:

* [Учетные данные Azure по умолчанию](#default-azure-credential)
* [Учетные данные управляемого удостоверения](#managed-identity-credential)

## <a name="default-azure-credential"></a>Учетные данные Azure по умолчанию

`DefaultAzureCredential` подходит для большинства сценариев, в которых приложение в конечном итоге выполняется в облаке Azure. `DefaultAzureCredential` объединяет учетные данные, которые обычно используются для аутентификации при развертывании, с учетными данными, используемыми для аутентификации в среде разработки. `DefaultAzureCredential` попытается выполнить проверку подлинности с помощью следующих механизмов по порядку.

![Поток проверки подлинности с помощью метода DefaultAzureCredential](./media/defaultazurecredential.svg)

* Среда. Метод `DefaultAzureCredential` будет считывать сведения об учетной записи, указанные с помощью [переменных среды](#environment-variables), и использовать их для проверки подлинности.
* Управляемое удостоверение. Если приложение развертывается на узле Azure с поддержкой управляемых удостоверений, `DefaultAzureCredential` будет выполнять проверку подлинности этой учетной записи.
* IntelliJ. Если проверка подлинности выполнена с помощью Azure Toolkit for IntelliJ, `DefaultAzureCredential` будет выполнять проверку подлинности этой учетной записи.
* Visual Studio Code. Если проверка подлинности выполнена с помощью подключаемого модуля учетной записи Azure для Visual Studio Code, `DefaultAzureCredential` будет выполнять проверку подлинности этой учетной записи.
* Azure CLI. Если проверка подлинности выполнена с помощью команды Azure CLI `az login`, `DefaultAzureCredential` будет выполнять проверку подлинности этой учетной записи.

### <a name="configure-defaultazurecredential"></a>Настройка DefaultAzureCredential

`DefaultAzureCredential` поддерживает набор конфигураций с помощью методов задания в `DefaultAzureCredentialBuilder` или переменных среды.

* Задайте переменные среды `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` и `AZURE_TENANT_ID`, как указано в разделе [Переменные среды](#environment-variables), чтобы настроить `DefaultAzureCredential` для проверки подлинности в качестве субъекта-службы, заданного значениями.
* Задайте `.managedIdentityClientId(String)` в построителе или переменной среды `AZURE_CLIENT_ID`, чтобы настроить `DefaultAzureCredential` для проверки подлинности в качестве определяемого пользователем управляемого удостоверения. Если не указать значение, проверка подлинности будет выполнена с помощью управляемого системой удостоверения.
* Задайте `.tenantId(String)` в построителе или переменной среды `AZURE_TENANT_ID`, чтобы настроить `DefaultAzureCredential` для проверки подлинности в определенном клиенте для общего кэша маркеров, Visual Studio Code и IntelliJ.
* Задайте переменную среды `AZURE_USERNAME`, чтобы настроить `DefaultAzureCredential` для выбора соответствующего кэшированного маркера из общего кэша маркеров.
* Задайте `.intelliJKeePassDatabasePath(String)` в построителе, чтобы настроить `DefaultAzureCredential` для чтения определенного файла KeePass при проверке подлинности с помощью учетных данных IntelliJ.

### <a name="authenticate-with-defaultazurecredential"></a>Проверка подлинности с помощью метода DefaultAzureCredential

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `DefaultAzureCredential`.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-a-user-assigned-managed-identity-with-defaultazurecredential"></a>Проверка подлинности назначенного пользователем управляемого удостоверения с помощью DefaultAzureCredential

В следующем примере демонстрируется проверка подлинности `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с использованием `DefaultAzureCredential`, развернутого в ресурсе Azure с помощью настроенного пользователем управляемого удостоверения.

```java
/**
 * The default credential will use the user-assigned managed identity with the specified client ID.
 */
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .managedIdentityClientId("<managed identity client ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

### <a name="authenticate-a-user-in-azure-toolkit-for-intellij-with-defaultazurecredential"></a>Проверка подлинности пользователя в Azure Toolkit for IntelliJ с помощью DefaultAzureCredential

В следующем примере демонстрируется проверка подлинности `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с использованием `DefaultAzureCredential` на рабочей станции, где установлена IntelliJ IDEA и пользователь выполнил вход с помощью учетной записи Azure в Azure Toolkit for IntelliJ.

Дополнительные сведения о настройке IntelliJ IDEA см. в статье [Вход в Azure Toolkit for IntelliJ для IntelliJCredential](identity-dev-env-auth.md#sign-in-azure-toolkit-for-intellij-for-intellijcredential).

```java
/**
 * The default credential will use the KeePass database path to find the user account in IntelliJ on Windows.
 */
// KeePass configuration is required only for Windows. No configuration needed for Linux / Mac.
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .intelliJKeePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

## <a name="managed-identity-credential"></a>Учетные данные управляемого удостоверения

Функция управляемого удостоверения проверяет подлинность управляемого удостоверения ресурса Azure (назначенного системой или пользователем). Таким образом, если приложение выполняется в ресурсе Azure, который поддерживает управляемые удостоверения с помощью конечных точек `IDENTITY/MSI` и `IMDS` или конечных точек обоих типов, используя эти учетные данные, можно будет выполнить проверку подлинности приложения. Это очень удобно при выполнении проверки подлинности без секрета.

См. сведения об [управляемых удостоверениях для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview).

### <a name="authenticate-in-azure-with-managed-identity"></a>Проверка подлинности в Azure на основе управляемых удостоверений

В следующем примере демонстрируется проверка подлинности `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `ManagedIdentityCredential` на виртуальной машине, в службе приложений, приложении-функции, Cloud Shell, Service Fabric, ARC или среде AKS в Azure с поддержкой управляемых удостоверений, назначаемых системой или пользователем.

```java
/**
 * Authenticate with a managed identity.
 */
ManagedIdentityCredential managedIdentityCredential = new ManagedIdentityCredentialBuilder()
  .clientId("<user-assigned managed identity client ID>") // required only for user-assigned
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(managedIdentityCredential)
  .buildClient();
```

## <a name="environment-variables"></a>Переменные среды

`DefaultAzureCredential` и `EnvironmentCredential` можно настроить с помощью переменных среды. Для каждого типа проверки подлинности требуются значения конкретных переменных:

### <a name="service-principal-with-secret"></a>Субъект-служба с секретом

| Имя переменной         | Значение                                                  |
| --------------------- | ------------------------------------------------------ |
| `AZURE_CLIENT_ID`     | Идентификатор приложения Azure Active Directory.           |
| `AZURE_TENANT_ID`     | Идентификатор клиента приложения Azure Active Directory. |
| `AZURE_CLIENT_SECRET` | Один из секретов клиента приложения.               |

### <a name="service-principal-with-certificate"></a>Субъект-служба с сертификатом

| Имя переменной         | Значение                                                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| `AZURE_CLIENT_ID`     | Идентификатор приложения Azure Active Directory.                                                         |
| `AZURE_TENANT_ID`     | Идентификатор клиента приложения Azure Active Directory.                                               |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Путь к файлу сертификата в кодировке PEM, включая закрытый ключ (без защиты паролем).|

### <a name="username-and-password"></a>Имя пользователя и пароль

| Имя переменной         | Значение                                            |
| --------------------- | ------------------------------------------------ |
| `AZURE_CLIENT_ID`     | Идентификатор приложения Azure Active Directory.     |
| `AZURE_USERNAME`      | Имя пользователя (обычно это адрес электронной почты).           |
| `AZURE_PASSWORD`      | Пароль, связанный с данным именем пользователя.  |

Настройка выполняется в указанном порядке. Например, если заданы значения секрета клиента и сертификата, используется секрет клиента.

## <a name="next-steps"></a>Следующие шаги

В этой статье рассматривается проверка подлинности приложений, размещенных в Azure. Эта форма проверки подлинности является лишь одним из нескольких методов аутентификации в Azure SDK для Java. В указанных ниже статьях описаны другие способы.

* [Аутентификация Azure в средах разработки](identity-dev-env-auth.md)
* [Аутентификация с помощью субъектов-служб](identity-service-principal-auth.md)
* [Аутентификация с помощью учетных данных пользователя](identity-user-auth.md)

После настройки аутентификации ознакомьтесь со статьей [Настройка ведения журнала в пакете Azure SDK для Java](logging-overview.md), чтобы получить сведения о возможностях ведения журнала, предоставляемых пакетом SDK

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
