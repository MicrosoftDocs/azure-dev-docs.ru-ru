---
title: Использование субъектов-служб Azure с помощью Azure CLI
description: Сведения о том, как создать субъекты-службы и использовать их с помощью Azure CLI.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 02/15/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: c18adbee84fd3e5c73367b07bbd0b03ac61008cd
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030736"
---
# <a name="create-an-azure-service-principal-with-the-azure-cli"></a>Создание субъекта-службы Azure с помощью Azure CLI

Разрешения для автоматизированных средств, которые используют службы Azure, всегда должны быть ограничены. В качестве замены входу в приложения с использованием учетной записи пользователя с полными правами Azure предлагает субъекты-службы.

Субъект-служба Azure — это удостоверение, созданное для использования с приложениями, размещенными службами и автоматизированными средствами с целью получения доступа к ресурсам Azure. Такой доступ ограничен ролями, которые назначены субъекту-службе, что позволяет управлять разрешениями доступа к ресурсам, в том числе на разных уровнях. По соображениям безопасности мы рекомендуем всегда использовать субъекты-службы с автоматизированными средствами, чтобы не допускать их входа с помощью удостоверения пользователя.

В этой статье описано, как создать субъект-службу, получить сведения о нем и сбросить учетные данные с помощью Azure CLI.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). При создании субъекта-службы вы задаете используемый им тип аутентификации для входа.

> [!NOTE]
>
> Если у вашей учетной записи нет прав на создание субъекта-службы, команда `az ad sp create-for-rbac` возвратит сообщение об ошибке, уведомляющее о том, что привилегий недостаточно для выполнения операции. Чтобы получить возможность создавать субъекты-службы, обратитесь к администратору Azure Active Directory.

Субъектам-службам доступно два вида аутентификации: на основе пароля и на основе сертификата.

### <a name="password-based-authentication"></a>Аутентификация на основе пароля

Если параметры аутентификации не указаны, используется аутентификация на основе пароля, который генерируется случайным образом.

  ```azurecli-interactive
  az ad sp create-for-rbac --name ServicePrincipalName
  ```

> [!IMPORTANT]
> Начиная с версии Azure CLI 2.0.68, параметр `--password` для создания субъекта-службы с пользовательским паролем __больше не поддерживается__, что должно предотвратить случайное использование ненадежных паролей.

Выходные данные для субъекта-службы с аутентификацией на основе пароля включают ключ `password`. __Обязательно__ скопируйте это значение, так как его нельзя получить повторно. Если вы забыли пароль, [сбросьте учетные данные для субъекта-службы](#reset-credentials).

Ключи `appId` и `tenant` отображаются в выходных данных команды `az ad sp create-for-rbac` и используются при аутентификации субъекта-службы.
Запишите их значения, но при необходимости вы можете получить их в любой момент с помощью команды [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="certificate-based-authentication"></a>Аутентификация на основе сертификата

Аргумент `--cert` позволяет воспользоваться аутентификацией на основе сертификата. При этом он требует указать существующий сертификат. Убедитесь, что все средства, которые используют этот субъект-службу, имеют доступ к закрытому ключу сертификата. Сертификаты должны иметь формат ASCII, например PEM, CER или DER. Передайте сертификат в виде строки или воспользуйтесь форматом `@path`, чтобы загрузить сертификат из файла.

> [!NOTE]
> В используемый PEM-файл нужно добавить **CERTIFICATE** к **PRIVATE KEY**.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert "-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----"
```

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert @/path/to/cert.pem
```

Чтобы использовать сертификат из Azure Key Vault, укажите аргумент `--keyvault`. В таком случае значение `--cert` будет именем сертификата.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert CertName --keyvault VaultName
```

Чтобы создать для аутентификации _самозаверяющий_ сертификат, укажите аргумент `--create-cert`:

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --create-cert
```

Выходные данные консоли:

```
Creating a role assignment under the scope of "/subscriptions/myId"
Please copy C:\myPath\myNewFile.pem to a safe place.
When you run 'az login', provide the file path in the --password argument
{
  "appId": "myAppId",
  "displayName": "myDisplayName",
  "fileWithCertAndPrivateKey": "C:\\myPath\\myNewFile.pem",
  "name": "http://myName",
  "password": null,
  "tenant": "myTenantId"
}
```

Содержимое нового PEM-файла:
```
-----BEGIN PRIVATE KEY-----
myPrivateKeyValue
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
myCertificateValue
-----END CERTIFICATE-----
```

> [!NOTE]
> Команда `az ad sp create-for-rbac --create-cert` создает субъект-службу и PEM-файл. PEM-файл содержит правильно отформатированные **PRIVATE KEY** и **CERTIFICATE**.

Вы можете добавить аргумент `--keyvault`, чтобы сохранить сертификат в Azure Key Vault. Если используется аргумент `--keyvault`, аргумент `--cert`__обязателен__.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --create-cert --cert CertName --keyvault VaultName
```

Выходные данные включают ключ `fileWithCertAndPrivateKey` (если только сертификат не хранится в Key Vault). Значение этого ключа позволяет определить, где хранится сгенерированный сертификат.
__Обязательно__ скопируйте сертификат в безопасное расположение. В противном случае вы не сможете войти с помощью этого субъекта-службы.

Для сертификатов, которые хранятся в Key Vault, получите закрытый ключ сертификата с помощью команды [az keyvault secret show](/cli/azure/keyvault/secret#az-keyvault-secret-show). В Key Vault имя секрета сертификата совпадает с именем самого сертификата. В случае утраты доступа к закрытому ключу сертификата [сбросьте учетные данные субъекта-службы](#reset-credentials).

Ключи `appId` и `tenant` отображаются в выходных данных команды `az ad sp create-for-rbac` и используются при аутентификации субъекта-службы.
Запишите их значения, но при необходимости вы можете получить их в любой момент с помощью команды [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

## <a name="get-an-existing-service-principal"></a>Получение существующего субъекта-службы

Список субъектов-служб в клиенте можно получить с помощью команды [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list). По умолчанию эта команда возвращает первые 100 субъектов-служб для вашего клиента. Чтобы получить все субъекты-службы клиента, укажите аргумент `--all`. На получение такого списка может потребоваться длительное время, поэтому мы рекомендуем отфильтровать список с помощью одного из следующих аргументов:

* `--display-name` — запрашивает субъекты-службы с _префиксом_, который совпадает с указанным именем. Отображаемое имя субъекта-службы представляет собой значение, заданное при создании с помощью параметра `--name`. Если вы не указали параметр `--name` при создании субъекта-службы, используется префикс имени `azure-cli-`.
* `--spn` — отфильтровывает точные совпадения имен субъектов-служб. Имя субъекта-службы всегда начинается с `https://`.
  Если значение, которое вы использовали для аргумента `--name`, не было универсальным кодом ресурса (URI), им будет значение `https://` с последующим отображаемым именем.
* `--show-mine` — запрашивает только те субъекты-службы, которые были созданы пользователем, вошедшим в систему.
* `--filter` — выполняет фильтрацию _на стороне сервера_ с использованием фильтра OData. Мы рекомендуем использовать этот метод, а не фильтрацию на стороне клиента с указанием аргумента CLI `--query`. Дополнительные сведения о фильтрах OData см. в статье [Синтаксис выражений OData для предложений фильтрации и упорядочивания в службе "Поиск Azure"](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

Для объектов субъектов-служб возвращаются подробные сведения. Чтобы получить только те сведения, которые нужны для входа, используйте строку запроса `[].{id:appId, tenant:appOwnerTenantId}`. Например, чтобы получить сведения для входа ото всех субъектов-служб, созданных пользователем, вошедшим в систему, выполните команду:

```azurecli-interactive
az ad sp list --show-mine --query "[].{id:appId, tenant:appOwnerTenantId}"
```

> [!IMPORTANT]
>
> Команды `az ad sp list` или [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show) позволяют получить данные о пользователе или клиенте, но не о секретах аутентификации _или_ способе аутентификации.
> Секреты для сертификатов в Key Vault можно извлечь с помощью команды [az keyvault secret show](/cli/azure/keyvault/secret#az-keyvault-secret-show), но по умолчанию другие секреты не сохраняются.
> Если вы забыли способ аутентификации или секрет, [сбросьте учетные данные субъекта-службы](#reset-credentials).

## <a name="manage-service-principal-roles"></a>Управление ролями субъекта-службы

В Azure CLI доступны следующие команды для управления назначением ролей:

* [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list);
* [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create);
* [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete).

По умолчанию субъекту-службе назначена роль **участника**. Эта роль имеет все разрешения на чтение из учетной записи Azure и запись в нее. Роль **читателя** имеет больше ограничений, предоставляя права доступа только на чтение.  Дополнительные сведения об управлении доступом на основе ролей см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

В этом примере мы добавим роль **читателя** и удалим роль **участника**.

```azurecli-interactive
az role assignment create --assignee APP_ID --role Reader
az role assignment delete --assignee APP_ID --role Contributor
```

> [!NOTE]
> Если ваша учетная запись не позволяет назначать роли, вы увидите сообщение об ошибке о том, что ваша учетная запись не авторизована для выполнения действия Microsoft.Authorization/roleAssignments/write. Чтобы получить возможность управлять ролями, обратитесь к администратору Azure Active Directory.

Добавление роли _не_ ограничивает назначенные ранее разрешения. При ограничении разрешений субъекта-службы обязательно удалите роль __участника__.

Чтобы проверить изменения, выведите назначенные роли:

```azurecli-interactive
az role assignment list --assignee APP_ID
```

## <a name="sign-in-using-a-service-principal"></a>Вход с помощью субъекта-службы

Войдите, чтобы протестировать разрешения и учетные данные нового субъекта-службы. Для входа с использованием субъекта-службы вам потребуются `appId`, `tenant` и учетные данные.

Чтобы войти с использованием субъекта-службы с помощью пароля:

```azurecli-interactive
az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
```

Чтобы войти с использованием сертификата, этот сертификат должен быть доступным локально как PEM- или DER-файл в формате ASCII. В используемый PEM-файл нужно добавить **CERTIFICATE** к **PRIVATE KEY**.

```azurecli-interactive
az login --service-principal --username APP_ID --tenant TENANT_ID --password /path/to/cert
```

Дополнительные сведения о входе с использованием субъекта-службы см. в статье [Вход с помощью Azure CLI](authenticate-azure-cli.md).

## <a name="reset-credentials"></a>Сброс учетных данных

Если вы забыли учетные данные для субъекта-службы, воспользуйтесь командой [az ad sp credential reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). Команда сброса принимает те же аргументы, что и команда `az ad sp create-for-rbac`.

```azurecli-interactive
az ad sp credential reset --name APP_ID
```
