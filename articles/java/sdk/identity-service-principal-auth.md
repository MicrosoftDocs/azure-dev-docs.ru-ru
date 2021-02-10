---
title: Проверка подлинности Azure с помощью субъекта-службы
description: Обзор основных понятий SDK Azure для Java, связанных с проверкой подлинности приложений с помощью субъекта-службы
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: fd8c107d4fb5d37e3f2c2e99ba45ad46a6de34ae
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528701"
---
# <a name="azure-authentication-with-service-principal"></a>Проверка подлинности Azure с помощью субъекта-службы

В этой статье описано поддержку библиотекой удостоверений Azure проверки подлинности маркеров Azure Active Directory с помощью субъекта-службы. В этой статье рассматриваются следующие темы:

* [Создание субъекта-службы с помощью Azure CLI](#create-a-service-principal-with-the-azure-cli)
* [Учетные данные секрета клиента](#client-secret-credential)
* [Учетные данные сертификата клиента](#client-certificate-credential)

Дополнительные сведения см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](/azure/active-directory/develop/app-objects-and-service-principals).

## <a name="create-a-service-principal-with-the-azure-cli"></a>Создание субъекта-службы с помощью Azure CLI

Чтобы создать или получить учетные данные секрета клиента, воспользуйтесь приведенными ниже примерами использования [Azure CLI][azure_cli].

Используйте следующую команду, чтобы создать субъект-службу и настроить для нее доступ к ресурсам Azure:

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment
```

Эта команда возвращает значение, аналогичное приведенному ниже.

```output
{
"appId": "generated-app-ID",
"displayName": "dummy-app-name",
"name": "http://dummy-app-name",
"password": "random-password",
"tenant": "tenant-ID"
}
```

Используйте следующую команду, чтобы создать субъект-службу, а также сертификат. Запишите путь и расположение этого сертификата.

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment --cert <certificate name> --create-cert
```

Проверьте возвращенные учетные данные и запишите следующие сведения:

* `AZURE\_CLIENT\_ID` для appId.
* `AZURE\_CLIENT\_SECRET` для пароля.
* `AZURE\_TENANT\_ID` для клиента.

## <a name="client-secret-credential"></a>Учетные данные секрета клиента

Эти учетные данные проходят проверку подлинности созданной субъект-службой через секрет клиента (пароль). В этом примере демонстрируется проверка подлинности `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `ClientSecretCredential`.

```java
/**
 *  Authenticate with client secret.
 */
ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
  .clientId("<your client ID>")
  .clientSecret("<your client secret>")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientSecretCredential)
  .buildClient();
```

## <a name="client-certificate-credential"></a>Учетные данные сертификата клиента

Эти учетные данные выполняют проверку подлинности созданной субъект-службы через секрет клиента (пароль). В этом примере демонстрируется проверка подлинности `SecretClient` из клиентской библиотеки [azure-security-keyvault-secrets][secrets_client_library] с помощью `ClientCertificateCredential`.

```java
/**
 *  Authenticate with a client certificate.
 */
ClientCertificateCredential clientCertificateCredential = new ClientCertificateCredentialBuilder()
  .clientId("<your client ID>")
  .pemCertificate("<path to PEM certificate>")
  // Choose between either a PEM certificate or a PFX certificate.
  //.pfxCertificate("<path to PFX certificate>", "PFX CERTIFICATE PASSWORD")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientCertificateCredential)
  .buildClient();
```

## <a name="next-steps"></a>Следующие шаги

В этой статье рассматривается проверка подлинности с помощью субъекта-службы. Эта форма проверки подлинности является лишь одним из нескольких методов аутентификации в Azure SDK для Java. В указанных ниже статьях описаны другие способы.

* [Аутентификация Azure в средах разработки](identity-dev-env-auth.md)
* [Аутентификация приложений, размещенных в Azure](identity-azure-hosted-auth.md)
* [Аутентификация с помощью учетных данных пользователя](identity-user-auth.md)

После настройки аутентификации ознакомьтесь со статьей [Настройка ведения журнала в пакете Azure SDK для Java](logging-overview.md), чтобы получить сведения о возможностях ведения журнала, предоставляемых пакетом SDK

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
