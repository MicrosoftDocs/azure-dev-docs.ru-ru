---
title: Краткое руководство. Использование секретов Azure Key Vault в рабочих процессах GitHub Actions
description: Использование секретов Key Vault в GitHub Actions для автоматизации рабочих процессов разработки программного обеспечения
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 02/04/2021
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 385889e067b375c80744c8f798d62c1b05468aab
ms.sourcegitcommit: 7287dff6bf4b30c2033924702c941bf520403e07
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99589723"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Использование секретов Key Vault в рабочих процессах GitHub Actions

Использование секретов Key Vault в [GitHub Actions](https://help.github.com/en/articles/about-github-actions) и безопасное хранение паролей и других секретов в хранилище ключей Azure. Дополнительные сведения см в статье о [Key Vault](/azure/key-vault/general/overview). 

Key Vault и GitHub Actions предоставляют средство централизованного управления секретами и все преимущества GitHub Actions. GitHub Actions — это набор функций в GitHub для автоматизации рабочих процессов разработки программного обеспечения. Вы можете развертывать рабочие процессы там же, где храните код, и вместе с другими пользователями создавать запросы на вытягивание и решать проблемы. 


## <a name="prerequisites"></a>Предварительные требования 
- Учетная запись GitHub. Если у вас ее нет, зарегистрируйтесь [бесплатно](https://github.com/join).  
- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Приложение Azure, подключенное к репозиторию GitHub. В этом примере используются инструкции из статьи [Развертывание контейнеров в Службе приложений Azure](/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Хранилище ключей Azure.  Azure Key Vault можно создать с помощью портала Azure, Azure CLI или Azure PowerShell.

## <a name="workflow-file-overview"></a>Общие сведения о файле рабочего процесса

Рабочий процесс определяется файлом YAML (.yml) по пути `/.github/workflows/` в вашем репозитории. Это определение содержит разные шаги и параметры рабочего процесса.

Для проверки подлинности с помощью GitHub Actions файл содержит два раздела:

|Section  |Задания  |
|---------|---------|
|**Аутентификация** | 1. Определение субъекта-службы. <br /> 2. Создание секрета GitHub. <br /> 3. Добавление назначения роли. |
|**хранилище ключей;** | 1. Добавление действия хранилища ключей. <br /> 2. Ссылка на секрет хранилища ключей. |

## <a name="authentication"></a>Аутентификация

Вы можете создать [субъект-службу](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) в [Azure CLI](/cli/azure/). Чтобы выполнить эту команду, откройте [Azure Cloud Shell](https://shell.azure.com/) на портале Azure или нажмите кнопку **Попробовать**.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

В указанном выше примере замените заполнители своим идентификатором подписки и именем группы ресурсов. Замените заполнитель `myApp` именем своего приложения. Выходные данные содержат объект JSON с учетными данными назначения роли, которые предоставляют доступ к приложению Службы приложений, как показано ниже. Скопируйте этот объект JSON для последующего использования. Вам понадобятся только разделы со значениями `clientId`, `clientSecret`, `subscriptionId` и `tenantId`. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Настройка секрета GitHub

Создайте секреты для учетных данных Azure, группы ресурсов и подписок. 

1. В [GitHub](https://github.com/) найдите нужный репозиторий.

1. Выберите **Settings > Secrets > New secret** (Параметры > Секреты > Новый секрет).

1. Вставьте все выходные данные JSON, полученные из команды Azure CLI, в поле значения секрета. Присвойте секрету имя `AZURE_CREDENTIALS`.

1. Скопируйте значение `clientId` для последующего использования. 

### <a name="add-a-role-assignment"></a>Добавление назначения роли 
 
Предоставьте доступ к субъекту-службе Azure, чтобы иметь возможность обращаться к хранилищу ключей для выполнения операций `get` и `list`. Если этого не сделать, вы не сможете использовать субъект-службу. 

Замените `keyVaultName` именем хранилища ключей, а `clientIdGUID` — значением `clientId`. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Использование действия хранилища ключей Azure

С помощью действия [хранилища ключей Azure](https://github.com/marketplace/actions/azure-key-vault-get-secrets) можно получить один или несколько секретов из экземпляра хранилища ключей Azure и использовать их в рабочих процессах GitHub Actions.

Полученные секреты задаются как выходные данные, а также как переменные среды. Когда переменные выводятся на консоль или в журналы, они автоматически скрываются.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Чтобы использовать хранилище ключей в рабочем процессе, необходимо иметь действие хранилища ключей и ссылку на это действие. 

В этом примере хранилище ключей называется `containervault`. С помощью действия хранилища ключей в среду добавляются два секрета хранилища ключей (`containerPassword` и `containerUsername`). 

На значения хранилища ключей позже будет ссылаться задача входа в Docker с префиксом `steps.myGetSecretAction.outputs`. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы закончите работу с приложением Azure, репозиторием GitHub и хранилищем ключей, очистите развернутые ресурсы, удалив группу ресурсов для приложения, репозитория GitHub и хранилища ключей.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Дополнительные сведения об Azure Key Vault](/azure/key-vault/general/overview)