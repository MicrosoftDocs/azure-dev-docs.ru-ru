---
title: Аутентификация в Azure с помощью Java и удостоверений Azure
description: Общие сведения об аутентификации и удостоверениях Azure SDK.
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 2a09b4ccaf39564c1cd2547417d722472ed9017c
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528671"
---
# <a name="azure-authentication-with-java-and-azure-identity"></a>Аутентификация в Azure с помощью Java и удостоверений Azure

В этой статье в общих чертах описана библиотека удостоверений Azure для Java, которая обеспечивает поддержку аутентификации Azure Active Directory на основе маркеров в Azure SDK для Java. Эта библиотека предоставляет набор реализаций `TokenCredential`, которые можно использовать для создания клиентов пакета Azure SDK, поддерживающих аутентификацию Azure AD на основе маркеров.

Библиотека удостоверений Azure сейчас поддерживает следующие возможности:

* [Аутентификация Azure в средах разработки Java](identity-dev-env-auth.md) с поддержкой таких типов аутентификации:
  * аутентификация IDEA IntelliJ с помощью данных для входа, получаемых из [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/);
  * аутентификация Visual Studio Code с помощью данных для входа, сохраненных в [подключаемом модуле Azure для Visual Studio Code](https://code.visualstudio.com/docs/azure/extensions);
  * аутентификация Azure CLI с помощью данных для входа, сохраненных в [Azure CLI](/cli/azure/what-is-azure-cli).
* [Аутентификация приложений, размещенных в Azure](identity-azure-hosted-auth.md) с поддержкой таких типов аутентификации:
  * аутентификация с помощью учетных данных Azure по умолчанию;
  * аутентификация с помощью управляемых удостоверений.
* [Аутентификация с помощью субъектов-служб](identity-service-principal-auth.md) с поддержкой таких типов аутентификации:
  * аутентификация с помощью секрета клиента;
  * Проверка подлинности с помощью сертификата клиента
* [Аутентификация с помощью учетных данных пользователя](identity-user-auth.md) с поддержкой таких типов аутентификации:
  * интерактивная аутентификация через браузер;
  * аутентификация с помощью кода устройства;
  * Проверка имени пользователя и пароля

Перейдите по приведенным выше ссылкам, чтобы узнать больше об особенностях каждого типа аутентификации. В остальной части статьи мы опишем часто используемые учетные данные `DefaultAzureCredential` и связанные темы.

## <a name="add-the-maven-dependencies"></a>Добавление зависимостей Maven

Чтобы добавить зависимость Maven, включите следующий код XML в файл проекта *pom.xml*. Замените номер версии *1.2.1* номером последней выпущенной версии, показанной на [странице клиентской библиотеки Microsoft Azure для удостоверений](https://mvnrepository.com/artifact/com.azure/azure-identity).

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.1</version>
</dependency>
```

## <a name="key-concepts"></a>Основные понятия

Чтобы получить представление о библиотеке удостоверений Azure, нужно ознакомиться с двумя основными понятиями: учетные данные и класс `DefaultAzureCredential`, который является популярной реализацией таких данных.

Учетные данные — это класс, который содержит или может получать данные, необходимые клиенту службы для аутентификации запросов. Клиенты служб в Azure SDK принимают учетные данные при их создании, а затем используют их для аутентификации запросов к службе.

Библиотека удостоверений Azure использует преимущественно аутентификацию OAuth с помощью Azure Active Directory и предлагает различные классы учетных данных, которые могут получить маркер Azure AD для аутентификации запросов служб. Все классы учетных данных в этой библиотеке являются реализациями абстрактного класса `TokenCredential` в [azure-core][azure_core_library]. Вы можете использовать любой из таких классов для создания клиентов служб, которые могут выполнять аутентификацию с помощью `TokenCredential`.

Класс `DefaultAzureCredential` подходит для большинства сценариев, в которых приложение предназначено для выполнения в облаке Azure. `DefaultAzureCredential` объединяет учетные данные, которые обычно используются для аутентификации при развертывании, с учетными данными, используемыми для аутентификации в среде разработки. Дополнительные сведения, в том числе примеры использования `DefaultAzureCredential`, см. в разделе [Учетные данные Azure по умолчанию](identity-azure-hosted-auth.md#default-azure-credential) статьи [Аутентификация приложений Java, размещенных в Azure](identity-azure-hosted-auth.md).

## <a name="examples"></a>Примеры

Как описано в статье [Использование пакета Azure SDK для Java](overview.md#provision-and-manage-azure-resources-with-management-libraries), между библиотеками управления есть некоторые различия. Одно из таких различий заключается в том, что существуют библиотеки для *использования* служб Azure, называемые клиентскими библиотеками, и библиотеки для *управления* службами Azure, называемые библиотеками управления. В следующих разделах представлен краткий обзор аутентификации в клиентских библиотеках и библиотеках управления.

### <a name="authenticate-azure-client-libraries"></a>Аутентификация в клиентских библиотеках Azure

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью класса `DefaultAzureCredential`.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-azure-management-libraries"></a>Аутентификация в библиотеках управления Azure

Библиотеки управления Azure используют те же AP-интерфейсы учетных данных, что и клиентские библиотеки Azure, но для управления ресурсами Azure в такой подписке также требуют указать [идентификатор подписки Azure](/learn/modules/create-an-azure-account/4-multiple-subscriptions).

Идентификаторы подписки можно найти на странице [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) на портале Azure. Для получения идентификаторов вы также можете выполнить следующую команду [Azure CLI][azure_cli]:

```azurecli
az account list --output table
```

Вы можете задать идентификатор подписки в переменной среды `AZURE_SUBSCRIPTION_ID`. Этот идентификатор `AzureProfile` принимает в качестве идентификатора подписки по умолчанию во время создания экземпляра `Manager`, как показано в следующем примере:

```java
AzureResourceManager azureResourceManager = AzureResourceManager.authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE))
    .withDefaultSubscription();
```

Класс `DefaultAzureCredential`, используемый в этом примере, выполняет аутентификацию экземпляра `AzureResourceManager` с помощью `DefaultAzureCredential`. Кроме того, вы можете использовать другие реализации Token Credential, предлагаемые в библиотеке удостоверений Azure, вместо класса `DefaultAzureCredential`.

## <a name="troubleshooting"></a>Устранение неполадок

Учетные данные вызывают исключения, если они не прошли аутентификацию или не позволяют ее выполнить. Если учетные данные не проходят аутентификацию, вызывается исключение `ClientAuthenticationException` с атрибутом `message`, описывающим причину сбоя аутентификации. Если `ChainedTokenCredential` вызывает это исключение, пошаговое выполнение базового списка учетных данных приостанавливается.

Если учетные данные не позволяют выполнить аутентификацию, так как один из обязательных для учетных данных базовых ресурсов недоступен на компьютере, вызывается исключение `CredentialUnavailableException` с атрибутом `message`. В нем описана причина, по которой учетные данные недоступны для аутентификации. Если `ChainedTokenCredential` вызывает исключение, в сообщение будут включены сообщения об ошибках для всех учетных данных в цепочке.

## <a name="next-steps"></a>Следующие шаги

В этой статье описана функциональность удостоверений Azure, реализованная в пакете Azure SDK для Java. Класс `DefaultAzureCredential` представлен как популярный класс, подходящий для множества случаев. В следующих статьях описаны другие способы аутентификации с помощью библиотеки удостоверений Azure, а также приведены дополнительные сведения о классе `DefaultAzureCredential`:

* [Аутентификация Azure в средах разработки](identity-dev-env-auth.md)
* [Аутентификация приложений, размещенных в Azure](identity-azure-hosted-auth.md)
* [Аутентификация с помощью субъектов-служб](identity-service-principal-auth.md)
* [Аутентификация с помощью учетных данных пользователя](identity-user-auth.md)

<!-- LINKS -->
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity
[aad_doc]: /azure/active-directory/
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[keys_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys
[logging]: https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
[eventhubs_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs
[azure_core_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core
[javadoc]: https://azure.github.io/azure-sdk-for-java
[jdk_link]: /java/azure/jdk
