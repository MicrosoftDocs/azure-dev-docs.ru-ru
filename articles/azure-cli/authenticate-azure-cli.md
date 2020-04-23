---
title: Вход с помощью Azure CLI
description: Интерактивный вход с помощью Azure CLI или вход с использованием локальных учетных данных
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 26112c9a7f338a7f178dc627d89f6e1f33d97714
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030866"
---
# <a name="sign-in-with-azure-cli"></a>Вход с помощью Azure CLI 

Azure CLI поддерживает несколько типов аутентификации. Проще всего приступить к работе можно с помощью оболочки [Azure Cloud Shell](/azure/cloud-shell/overview), которая автоматически выполняет вход в вашу учетную запись.
На локальном компьютере можно выполнить интерактивную процедуру входа в браузере с помощью команды [az login](/cli/azure/reference-index#az-login). При написании скриптов рекомендуется использовать субъекты-службы. Предоставляя именно те разрешения, которые нужны субъекту-службе, можно обеспечить безопасность скрипта автоматизации.

CLI не хранит ваши сведения о входе. [Маркер обновления аутентификации](https://docs.microsoft.com/azure/active-directory/develop/v1-id-and-access-tokens#refresh-tokens) создается и сохраняется в Azure. Начиная с августа 2018 г., маркер становится недействительным в случае бездействия пользователя в течение 90 дней. Этот срок может быть изменен корпорацией Майкрософт или администратором клиента. Если маркер становится недействительным, в CLI появится сообщение о необходимости повторно войти в учетную запись.

После входа команды CLI будут выполняться в вашей подписке по умолчанию. Если у вас есть несколько подписок, можно [изменить подписку по умолчанию](manage-azure-subscriptions-azure-cli.md).

## <a name="sign-in-interactively"></a>Интерактивный вход

Метод проверки подлинности, используемый в Azure CLI по умолчанию, — вход с помощью веб-браузера и маркера доступа.

[!INCLUDE [interactive_login](includes/interactive-login.md)]

## <a name="sign-in-with-credentials-on-the-command-line"></a>Вход с учетными данными с помощью командной строки

Укажите свои учетные данные пользователя Azure в командной строке.

> [!Note]
> Этот способ не работает с учетными записями Майкрософт или с учетными записями, которые используют двухфакторную проверку подлинности.

```azurecli-interactive
az login -u <username> -p <password>
```

> [!IMPORTANT]
> Если вы не хотите, чтобы ваш пароль отображался в консоли, и используете `az login` в интерактивном режиме, используйте команду `read -s` в `bash`.
>
> ```bash
> read -sp "Azure password: " AZ_PASS && echo && az login -u <username> -p $AZ_PASS
> ```
>
> В PowerShell используйте командлет `Get-Credential`.
>
> ```powershell
> $AzCred = Get-Credential -UserName <username>
> az login -u $AzCred.UserName -p $AzCred.GetNetworkCredential().Password
> ```

## <a name="sign-in-with-a-service-principal"></a>Вход с использованием субъекта-службы

Субъекты-службы — это учетные записи, не связаны с определенным пользователем. Они предоставляют разрешения, назначаемые с помощью предопределенных ролей. Аутентификация с помощью субъекта-службы лучше всего подходит для создания безопасных скриптов и программ, позволяя применять как ограничения разрешений, так и хранимые локально сведения о статических учетных данных. Дополнительные сведения о субъектах-службах см. в руководстве по [созданию субъекта-службы Azure с помощью Azure CLI ](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal).

Для входа с помощью субъекта-службы необходимы следующие сведения:

* URL-адрес или имя, связанное с субъектом-службой;
* пароль субъекта-службы или сертификат X509 в формате PEM, используемый для создания субъекта-службы;
* идентификатор клиента, связанного с субъектом-службой, в виде домена `.onmicrosoft.com` или идентификатора объекта Azure.

> [!NOTE]
> В PEM-файле нужно добавить **CERTIFICATE** к **PRIVATE KEY**.  См. пример формата PEM-файла в руководстве по [созданию субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal). 
>

> [!IMPORTANT]
>
> Если ваш субъект-служба использует сертификат, который хранится в Key Vault, закрытый ключ такого сертификата должен быть доступен без входа в Azure. Чтобы получить закрытый ключ для использования в автономном режиме, выполните команду [az keyvault secret show](/cli/azure/keyvault/secret).

```azurecli-interactive
az login --service-principal -u <app-url> -p <password-or-cert> --tenant <tenant>
```

> [!IMPORTANT]
> Если вы не хотите, чтобы ваш пароль отображался в консоли, и используете `az login` в интерактивном режиме, используйте команду `read -s` в `bash`.
>
> ```bash
> read -sp "Azure password: " AZ_PASS && echo && az login --service-principal -u <app-url> -p $AZ_PASS --tenant <tenant>
> ```
>
> В PowerShell используйте командлет `Get-Credential`.
>
> ```powershell
> $AzCred = Get-Credential -UserName <app-url>
> az login --service-principal -u $AzCred.UserName -p $AzCred.GetNetworkCredential().Password --tenant <tenant>
> ```

## <a name="sign-in-with-a-different-tenant"></a>Вход с помощью другого клиента

С помощью аргумента `--tenant` можно выбрать клиент, от имени которого выполняется вход. Значением этого аргумента может быть домен `.onmicrosoft.com` или идентификатор объекта Azure для клиента. Аргумент `--tenant` поддерживает оба метода входа — интерактивный и с помощью командной строки.

```azurecli-interactive
az login --tenant <tenant>
```

## <a name="sign-in-with-a-managed-identity"></a>Вход с помощью управляемого удостоверения

В ресурсы Azure, которые настроены для использования управляемых удостоверений, можно выполнить вход с помощью такого удостоверения. Чтобы выполнить вход с помощью удостоверения ресурса, используйте флаг `--identity`.

```azurecli-interactive
az login --identity
```

Дополнительные сведения об управляемых удостоверениях для ресурсов Azure см. в статьях о [настройке управляемых удостоверений для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) и [использовании управляемых удостоверений для входа в ресурсы Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in).
