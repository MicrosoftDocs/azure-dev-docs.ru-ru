---
title: Аутентификация Azure в средах разработки Java
description: Общие сведения об основных понятиях Azure SDK для Java, имеющих отношение к аутентификации в средах разработки.
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 7913430a81a0fe223d6eb23a48541e0f752323b2
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528680"
---
# <a name="azure-authentication-in-java-development-environments"></a>Аутентификация Azure в средах разработки Java

В этой статье в общих чертах описана поддержка, которая предоставляется библиотекой удостоверений Azure для аутентификации с помощью маркеров Azure AD. Эта поддержка позволяет использовать аутентификацию для приложений, выполняемых локально на компьютерах разработки, с помощью набора реализаций `TokenCredential`.

В этой статье рассматриваются следующие темы:

* [учетные данные для входа с помощью кода устройства](#device-code-credential);
* [учетные данные для входа интерактивно через браузер](#interactive-browser-credential);
* [учетные данные для входа с помощью Azure CLI](#azure-cli-credential);
* [учетные данные для входа с помощью IntelliJ](#intellij-credential);
* [учетные данные для входа с помощью Visual Studio Code](#visual-studio-code-credential).

## <a name="device-code-credential"></a>Учетные данные для входа с помощью кода устройства

Учетные данные для входа с помощью кода устройства позволяют интерактивно выполнять аутентификацию пользователя на устройствах с ограниченным пользовательским интерфейсом. Для этого, когда приложение пытается выполнить проверку подлинности, пользователю предлагается перейти по URL-адресу для входа на компьютере с поддержкой браузера. Затем пользователь вводит код устройства, указанный в инструкциях, а также учетные данные для входа. После успешной аутентификации приложение, которое запросило аутентификацию, успешно пройдет проверку на устройстве, на котором оно выполняется.

Дополнительные сведения см. в статье [Платформа удостоверений Майкрософт и поток предоставления авторизации устройства OAuth 2.0](/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="enable-applications-for-device-code-flow"></a>Настройка потока кода устройства для приложений

Чтобы настроить аутентификацию пользователя в потоке кода устройства, выполните следующие действия:

1. На портале Azure перейдите к странице Azure Active Directory и найдите регистрацию нужного приложения.
2. Перейдите к разделу **Проверка подлинности**.
3. В разделе **Suggested Redirected URIs** (Предлагаемые перенаправленные URI) проверьте универсальный код ресурса (URI), который заканчивается на `/common/oauth2/nativeclient`.
4. В разделе **Тип клиента по умолчанию** выберите *Да* для поля **Считать приложение общедоступным клиентом**.

Эти шаги позволят приложению выполнять аутентификацию, но оно по-прежнему не будет иметь разрешения на вход в Active Directory или доступ к ресурсам от вашего имени. Чтобы устранить эту проблему, перейдите к разделу **Разрешения API** и включите Microsoft Graph и ресурсы, к которым требуется доступ.

Кроме того, вам потребуется роль администратора арендатора, чтобы предоставить приложению согласие при первом входе.

Если вы не можете настроить поток кода устройства в Active Directory, может потребоваться организовать поддержку нескольких арендаторов в приложении. Чтобы обеспечить в приложении поддержку нескольких арендаторов, перейдите на панель **Проверка подлинности**, а затем выберите **Учетные записи в любом каталоге организации**. Затем выберите значение *Да* для параметра **Treat application as Public Client** (Считать приложение общедоступным клиентом).

### <a name="authenticate-a-user-account-with-device-code-flow"></a>Проверка подлинности учетной записи пользователя в потоке кода устройства

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью класса `DeviceCodeCredential` на устройстве Интернета вещей.

```java
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
  .challengeConsumer(challenge -> {
    // lets user know of the challenge
    System.out.println(challenge.getMessage());
  }).build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(deviceCodeCredential)
  .buildClient();
```

## <a name="interactive-browser-credential"></a>Учетные данные для входа интерактивно через браузер

Эти учетные данные позволяют интерактивно выполнять аутентификацию пользователя через системный браузер по умолчанию и обеспечивают беспроблемность аутентификации, позволяя вам использовать собственные учетные данные для аутентификации приложения.

### <a name="enable-applications-for-interactive-browser-oauth-2-flow"></a>Настройка потока OAuth 2 для входа приложений интерактивно через браузер

Чтобы использовать `InteractiveBrowserCredential`, нужно зарегистрировать приложение в Azure Active Directory с разрешениями на вход от имени пользователя. Выполните описанные выше действия, чтобы поток кода устройства зарегистрировал приложение. Как упоминалось ранее, администратор арендатора должен предоставить приложению согласие, прежде чем любая учетная запись пользователя сможет войти в систему.

Вы можете обратить внимание, что в `InteractiveBrowserCredentialBuilder` требуется URL-адрес перенаправления. Добавьте URL-адрес перенаправления в подраздел **Перенаправление URI** в разделе **Проверка подлинности** зарегистрированного в Azure AD приложения.

### <a name="authenticate-a-user-account-interactively-in-the-browser"></a>Интерактивная проверка подлинности учетной записи пользователя в браузере

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `InteractiveBrowserCredential`.

```java
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
  .clientId("<your client ID>")
  .redirectUrl("http://localhost:8765")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(interactiveBrowserCredential)
  .buildClient();
```

## <a name="azure-cli-credential"></a>Учетные данные для входа с помощью Azure CLI

Учетные данные Visual Studio Code позволяют выполнять аутентификацию в среде разработки с включенным пользователем или субъектом-службой с помощью Azure CLI. Они применяются в Azure CLI для пользователя, который уже выполнил вход в систему, и в интерфейсе командной строки для аутентификации приложения в Azure Active Directory.

### <a name="sign-in-azure-cli-for-azureclicredential"></a>Вход Azure CLI для AzureCliCredential

Войдите в систему от имени пользователя, используя следующую команду [Azure CLI][azure_cli]:

```bash
az login
```

Войдите в систему от имени субъекта-службы, используя следующую команду:

```bash
az login --service-principal --username <client ID> --password <client secret> --tenant <tenant ID>
```

Если у этой учетной записи или субъекта-службы есть доступ к нескольким арендаторам, убедитесь, что в выходных данных следующей команды состояние Enabled (Включено) указано для нужного арендатора или подписки:

```bash
az account list
```

Прежде чем применять `AzureCliCredential` в коде, выполните следующую команду и убедитесь, что учетная запись успешно настроена.

```bash
az account get-access-token
```

Возможно, этот процесс придется повторить по истечении некоторого периода времени в зависимости от срока действия маркеров обновления в организации. Как правило, период действия маркера обновления составляет от нескольких недель до нескольких месяцев. `AzureCliCredential` предложит вам снова войти в систему.

### <a name="authenticate-a-user-account-with-azure-cli"></a>Аутентификация учетной записи пользователя с помощью Azure CLI

В примере ниже демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `AzureCliCredential` на рабочей станции, где установлен Azure CLI и выполнен вход в систему.

```java
AzureCliCredential cliCredential = new AzureCliCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(cliCredential)
  .buildClient();
```

## <a name="intellij-credential"></a>Учетные данные для входа с помощью IntelliJ

Учетные данные Visual Studio Code позволяют выполнять аутентификацию в среде разработки с помощью учетной записи в Azure Toolkit for IntelliJ. Они используют данные пользователя, вошедшего в систему в интегрированной среде разработки IntelliJ, для аутентификации приложения в Azure Active Directory.

## <a name="sign-in-azure-toolkit-for-intellij-for-intellijcredential"></a>Вход Azure Toolkit for IntelliJ для IntelliJCredential

Выполните следующие шаги:

1. В окне IntelliJ откройте **File > Settings > Plugins** (Файл — Параметры — Подключаемые модули).
1. Выполните в Marketplace поиск по строке "Azure Toolkit for IntelliJ". Установите и перезапустите интегрированную среду разработки.
1. Найдите новый пункт меню **Tools > Azure > Azure Sign In…** (Средства — Azure — Вход в Azure).
1. **Вход устройства** позволяет войти в систему с учетной записью пользователя. Следуйте инструкциям, чтобы войти на веб-сайт `login.microsoftonline.com` с помощью кода устройства. IntelliJ предложит вам выбрать подписки. Выберите те из них, где находятся нужные ресурсы.

В среде Windows вам также потребуется путь к базе данных KeePass, чтобы получить учетные данные IntelliJ. Этот путь можно найти в параметрах IntelliJ в разделе **File > Settings > Appearance & Behavior > System Settings > Passwords** (Файл — Параметры — Внешний вид и поведение — Параметры системы — Пароли). Запишите расположение пути KeePassDatabase.

## <a name="authenticate-a-user-account-with-intellij-idea"></a>Аутентификация учетной записи пользователя с помощью IntelliJ IDEA

В следующем примере демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `IntelliJCredential`, на рабочей станции, где установлена IntelliJ IDEA и пользователь выполнил вход с помощью учетной записи Azure.

```java
IntelliJCredential intelliJCredential = new IntelliJCredentialBuilder()
  // KeePass configuration isrequired only for Windows. No configuration needed for Linux / Mac.
  .keePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(intelliJCredential)
  .buildClient();
```

## <a name="visual-studio-code-credential"></a>Учетные данные для входа с помощью Visual Studio Code

Учетные данные Visual Studio Code позволяют выполнять аутентификацию в средах разработки, где установлен VS Code вместе с [расширением учетной записи Azure для VS Code](https://github.com/Microsoft/vscode-azure-account). Они используют данные пользователя, вошедшего в систему в интегрированной среде разработки VS Code, для аутентификации приложения в Azure Active Directory.

### <a name="sign-in-visual-studio-code-azure-account-extension-for-visualstudiocodecredential"></a>Вход в расширение учетной записи Azure для Visual Studio Code для VisualStudioCodeCredential

Аутентификация Visual Studio Code выполняется с помощью интеграции с [расширением учетной записи Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Чтобы использовать эту форму аутентификации, установите расширение учетной записи Azure и выберите **View > Command Palette** (Вид — Палитра команд) для запуска команды **Azure: вход**. Эта команда открывает окно браузера и отображает страницу для входа в Azure. После завершения процесса входа вы можете закрыть браузер, о чем появится соответствующее сообщение. При запуске приложения (в отладчике или из любого расположения на компьютере разработки) эти учетные данные будут использоваться для входа в систему.

### <a name="authenticate-a-user-account-with-visual-studio-code"></a>Аутентификация учетной записи пользователя с помощью Visual Studio Code

В следующем примере демонстрируется аутентификация `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `VisualStudioCodeCredential`, на рабочей станции, где установлен Visual Studio Code и пользователь выполнил вход с помощью учетной записи Azure.

```java
VisualStudioCodeCredential visualStudioCodeCredential = new VisualStudioCodeCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(visualStudioCodeCredential)
  .buildClient();
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы изучили аутентификацию на этапе разработки с использованием учетных данных, доступных на локальном компьютере. Эта форма проверки подлинности является лишь одним из нескольких методов аутентификации в Azure SDK для Java. В указанных ниже статьях описаны другие способы.

* [Аутентификация приложений, размещенных в Azure](identity-azure-hosted-auth.md)
* [Аутентификация с помощью субъектов-служб](identity-service-principal-auth.md)
* [Аутентификация с помощью учетных данных пользователя](identity-user-auth.md)

После настройки аутентификации ознакомьтесь со статьей [Настройка ведения журнала в пакете Azure SDK для Java](logging-overview.md), чтобы получить сведения о возможностях ведения журнала, предоставляемых пакетом SDK

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
