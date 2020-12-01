---
title: Подключение GitHub к Azure
description: Ресурсы для подключения к GitHub из Azure и других служб
author: N-Usha
ms.author: ushan
ms.topic: reference
ms.service: azure
ms.date: 11/17/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 5462d7ca3618869232296a9a6739ebe5adcefdb1
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94983633"
---
# <a name="use-github-actions-to-connect-to-azure"></a>Применение GitHub Actions для подключения к Azure

Узнайте, как выполнять [вход в Azure](https://github.com/Azure/login) через [Azure PowerShell](https://github.com/Azure/PowerShell) или [Azure CLI](https://github.com/Azure/CLI) для взаимодействия с ресурсами Azure.

Чтобы использовать Azure PowerShell или Azure CLI в рабочем процессе GitHub Actions, вам нужно выполнить действие [входа в Azure](https://github.com/marketplace/actions/azure-login).
Действие входа в Azure позволяет выполнять команды в рабочем процессе в контексте [субъекта-службы Azure AD](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).

Настроив действие входа, вы сможете применить его в Azure CLI или Azure PowerShell.

По умолчанию действие выполняет вход с помощью Azure CLI, подготавливая среду выполнения действий GitHub для Azure CLI. Вы можете использовать Azure PowerShell со свойством `enable-AzPSSession` действия входа в Azure. Оно обеспечивает настройку среды выполнения действий GitHub с модулем Azure PowerShell.

## <a name="create-a-service-principal-and-add-it-to-github-secret"></a>Создание субъекта-службы и его добавление в секрет GitHub

Чтобы использовать [вход в Azure](https://github.com/marketplace/actions/azure-login), сначала нужно добавить субъект-службу Azure в качестве секрета в репозиторий GitHub.

В этом примере вы создадите секрет с именем `AZURE_CREDENTIALS`, который можно применить для аутентификации в Azure.  

1. Если у вас нет существующего приложения, зарегистрируйте [новое приложение Active Directory](/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal&preserve-view=true) для использования с субъектом-службой.

    ```azurecli-interactive
        appName="myApp"

        az ad app create \
        --display-name $appName \
        --homepage "http://localhost/$appName" \
        --identifier-uris http://localhost/$appName
    ```

1. [Создайте субъект-службу](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true) для приложения на портале Azure. 

    ```azurecli-interactive
        az ad sp create-for-rbac --name "myApp" --role contributor \
                                    --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                    --sdk-auth
    ```

1. Скопируйте объект JSON для субъекта-службы.

    ```json
    {
        "clientId": "<GUID>",
        "clientSecret": "<GUID>",
        "subscriptionId": "<GUID>",
        "tenantId": "<GUID>",
        (...)
    }
    ```

1. Откройте репозиторий GitHub и перейдите в раздел **Параметры**.

    :::image type="content" source="media/github-repo-settings.png" alt-text="Выбор раздела &quot;Параметры&quot; в области навигации":::

1. Выберите **Секреты**, а затем **Создать секрет**.

    :::image type="content" source="media/select-secrets.png" alt-text="Выбор действия добавления секрета":::

1. Вставьте объект JSON субъекта-службы с именем `AZURE_CREDENTIALS`. 

    :::image type="content" source="media/azure-secret-add.png" alt-text="Добавление секрета в GitHub":::

1. Сохраните секрет, выбрав действие **Добавить секрет**.

## <a name="use-the-azure-login-action"></a>Использование действия входа в Azure

Примените секрет субъекта-службы вместе с [действием входа в Azure](https://github.com/Azure/login) для аутентификации в Azure.

В этом рабочем процессе вы выполните аутентификацию с помощью действия входа в Azure с применением сохраненных в `secrets.AZURE_CREDENTIALS` сведений о субъекте-службе. Затем вы выполните действие Azure CLI. Дополнительные сведения о ссылках на секреты GitHub в файле рабочего процесса см. в статье об [использовании зашифрованных секретов в рабочем процессе](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow) на портале GitHub Docs.

Получив работающее действие входа в Azure, вы сможете использовать действия [Azure PowerShell](https://github.com/Azure/PowerShell) или [Azure CLI](https://github.com/Azure/CLI). Вы также сможете использовать другие действия Azure, например [развертывание веб-приложения Azure](https://github.com/Azure/webapps-deploy) и [функции Azure](https://github.com/Azure/functions-action).

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
```

## <a name="use-the-azure-powershell-action"></a>Использование действия Azure PowerShell

В этом примере показано, как выполнить вход с помощью [действия входа в Azure](https://github.com/Azure/login), а затем извлечь группу ресурсов с помощью [действия Azure PowerShell](https://github.com/azure/powershell).

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
          enable-AzPSSession: true
      - name: Azure PowerShell Action
        uses: Azure/powershell@v1
        with:
          inlineScript: Get-AzVM -ResourceGroupName "< YOUR RESOURCE GROUP >"
          azPSVersion: 3.1.0
```

## <a name="use-the-azure-cli-action"></a>Использование действия Azure CLI

В этом примере вы войдете в систему с помощью [действия входа в Azure](https://github.com/Azure/login), а затем извлечете группу ресурсов с помощью [действия Azure CLI](https://github.com/Azure/CLI).


```yaml
on: [push]

name: AzureLoginSample

jobs:
build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - name: Log in with Azure
        uses: azure/login@v1
        with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
        uses: azure/CLI@v1
        with:
        azcliversion: 2.0.72
        inlineScript: |
            az account show
            az storage -h
```

## <a name="connect-with-other-azure-services"></a>Подключение к другим службам Azure

В следующих статьях содержатся сведения о подключении к GitHub из Azure и других служб.  

### <a name="azure-active-directory"></a>Azure Active Directory 

- [Руководство. Интеграция единого входа Azure Active Directory с GitHub](/azure/active-directory/saas-apps/github-tutorial)   

### <a name="power-bi"></a>Power BI

- [Подключение Power BI к GitHub](/power-bi/service-connect-to-github)   

### <a name="connectors"></a>Соединители

- [Соединитель GitHub для Azure Logic Apps, Power Automate и Power Apps](/connectors/github/)   

### <a name="azure-databricks"></a>Azure Databricks

- [Управление версиями GitHub](/azure/databricks/notebooks/github-version-control) 

> [!div class="nextstepaction"]
> [Развертывание приложений из GitHub в Azure](deploy-to-azure.md)
