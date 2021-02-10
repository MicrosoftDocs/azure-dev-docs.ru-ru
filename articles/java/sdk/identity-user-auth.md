---
title: Аутентификация в Azure с помощью учетных данных пользователя
description: Обзор основных понятий SDK Azure для Java, связанных с проверкой подлинности приложений с помощью учетных данных пользователя
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: d2b5b24b0f39d56ca2235ec56a4ea56c4be6b185
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528674"
---
# <a name="azure-authentication-with-user-credentials"></a>Аутентификация в Azure с помощью учетных данных пользователя

В этой статье описано поддержку библиотекой удостоверений Azure проверки подлинности маркеров Azure Active Directory с помощью предоставленных пользователем учетных данных. Эта поддержка реализуется с помощью набора реализаций TokenCredential, описанных ниже.

В этой статье рассматриваются следующие темы:

* [учетные данные для входа с помощью кода устройства](#device-code-credential);
* [учетные данные для входа интерактивно через браузер](#interactive-browser-credential);
* [учетные данные пароля и имени пользователя](#username-password-credential).

## <a name="device-code-credential"></a>Учетные данные для входа с помощью кода устройства

Учетные данные для входа с помощью кода устройства позволяют интерактивно выполнять аутентификацию пользователя на устройствах с ограниченным пользовательским интерфейсом. Для этого, когда приложение пытается выполнить проверку подлинности, пользователю предлагается перейти по URL-адресу для входа на компьютере с поддержкой браузера. Затем пользователь вводит код устройства, указанный в инструкциях, а также учетные данные для входа. После успешной аутентификации приложение, которое запросило аутентификацию, успешно пройдет проверку на устройстве, на котором оно выполняется.

Дополнительные сведения см. в статье [Платформа удостоверений Майкрософт и поток предоставления авторизации устройства OAuth 2.0](/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="enable-applications-for-device-code-flow"></a>Настройка потока кода устройства для приложений

Чтобы настроить проверку подлинности пользователя в потоке кода устройства, выполните следующие шаги.

1. На портале Azure перейдите к странице Azure Active Directory и найдите регистрацию нужного приложения.
2. Перейдите к разделу **Проверка подлинности**.
3. В разделе **Suggested Redirected URIs** (Предлагаемые перенаправленные URI) проверьте универсальный код ресурса (URI), который заканчивается на `/common/oauth2/nativeclient`.
4. В разделе **Тип клиента по умолчанию** выберите `yes` для `Treat application as a public client`.

Эти шаги позволят приложению выполнять аутентификацию, но оно по-прежнему не будет иметь разрешения на вход в Active Directory или доступ к ресурсам от вашего имени. Чтобы устранить эту ошибку, перейдите к **разрешениям API** и настройте Microsoft Graph и ресурсы, к которым требуется доступ, например управление службами Azure, Key Vault и т. д.

Кроме того, вам потребуется роль администратора клиента, чтобы предоставить приложению согласие при первом входе.

Если вы не можете настроить поток кода устройства в Active Directory, может потребоваться организовать поддержку нескольких арендаторов в приложении. Чтобы обеспечить в приложении поддержку нескольких арендаторов, перейдите на панель **Проверка подлинности**, а затем выберите **Учетные записи в любом каталоге организации**. Затем выберите значение *Да* для параметра **Treat application as Public Client** (Считать приложение общедоступным клиентом).

### <a name="authenticate-a-user-account-with-device-code-flow"></a>Проверка подлинности учетной записи пользователя в потоке кода устройства

В примере ниже демонстрируется проверка подлинности `SecretClient` из [клиентской библиотеки секретов Azure Key Vault для Java][secrets_client_library] с помощью `DeviceCodeCredential` на устройстве Интернета вещей.

```java
/**
 * Authenticate with device code credential.
 */
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
    .challengeConsumer(challenge -> {
    // Lets the user know about the challenge.
    System.out.println(challenge.getMessage());
    }).build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(deviceCodeCredential)
    .buildClient();
```

## <a name="interactive-browser-credential"></a>Учетные данные для входа интерактивно через браузер

Эти учетные данные позволяют интерактивно выполнять проверку подлинности пользователя через системный браузер по умолчанию и обеспечивают легкую проверку подлинности, позволяя использовать собственные учетные данные для проверки подлинности приложения.

### <a name="enable-applications-for-interactive-browser-oauth-2-flow"></a>Настройка потока OAuth 2 для входа приложений интерактивно через браузер

Чтобы использовать `InteractiveBrowserCredential`, нужно зарегистрировать приложение в Azure Active Directory с разрешениями на вход от имени пользователя. Выполните описанные выше действия, чтобы поток кода устройства зарегистрировал приложение. Как упоминалось ранее, администратор арендатора должен предоставить приложению согласие, прежде чем любая учетная запись пользователя сможет войти в систему.

Вы можете заметить, что в `InteractiveBrowserCredentialBuilder`требуется URL-адрес перенаправления. Добавьте URL-адрес перенаправления в подраздел **Перенаправление URI** в разделе **Проверка подлинности** зарегистрированного в Azure AD приложения.

### <a name="authenticate-a-user-account-interactively-in-the-browser"></a>Интерактивная проверка подлинности учетной записи пользователя в браузере

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `InteractiveBrowserCredential`.

```java
/**
 * Authenticate interactively in the browser.
 */
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
    .clientId("<your app client ID>")
    .redirectUrl("YOUR_APP_REGISTERED_REDIRECT_URL")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(interactiveBrowserCredential)
    .buildClient();
```

## <a name="username-password-credential"></a>Учетные данные пароля и имени пользователя

`UsernamePasswordCredential` позволяет проверить подлинность общедоступного клиентского приложения, используя учетные данные пользователя, которые не нуждаются в многофакторной проверке подлинности. В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `UsernamePasswordCredential`. Для пользователя не должна быть включена многофакторная проверка подлинности.

```java
/**
 * Authenticate with username, password.
 */
UsernamePasswordCredential usernamePasswordCredential = new UsernamePasswordCredentialBuilder()
    .clientId("<your app client ID>")
    .username("<your username>")
    .password("<your password>")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(usernamePasswordCredential)
    .buildClient();
```

Дополнительные сведения см. в статье [Учетные данные владельца ресурса OAuth 2.0 для платформы удостоверений Майкрософт](/azure/active-directory/develop/v2-oauth-ropc).

## <a name="next-steps"></a>Следующие шаги

В этой статье рассматривается аутентификация с помощью учетных данных пользователя. Эта форма проверки подлинности является лишь одним из нескольких методов аутентификации в Azure SDK для Java. В указанных ниже статьях описаны другие способы.

* [Аутентификация Azure в средах разработки](identity-dev-env-auth.md)
* [Аутентификация приложений, размещенных в Azure](identity-azure-hosted-auth.md)
* [Аутентификация с помощью субъектов-служб](identity-service-principal-auth.md)

После настройки аутентификации ознакомьтесь со статьей [Настройка ведения журнала в пакете Azure SDK для Java](logging-overview.md), чтобы получить сведения о возможностях ведения журнала, предоставляемых пакетом SDK

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
