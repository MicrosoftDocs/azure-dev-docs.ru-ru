---
title: Задачи автоматизации веб-приложений с помощью Azure CLI
description: Автоматизация задач Azure — это частое требование для непрерывного развертывания в средах размещения. Azure CLI является рекомендуемым вариантом для разработчиков JavaScript, управляющих задачами и выполняющих развертывание из любого расположения.
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7cfce90d8d0daf861dab9ba02e46ce489ae10742
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754060"
---
# <a name="automate-tasks-with-azure-cli"></a>Автоматизация задач с помощью Azure CLI

Автоматизация задач Azure — это частое требование для непрерывного развертывания в средах размещения. [Azure CLI](/cli/azure/) является рекомендуемым вариантом для разработчиков JavaScript, управляющих задачами и выполняющих развертывание из любого расположения.

Узнайте о командах распространенных задач для разработчиков JavaScript. 

## <a name="automation-with-azure-cli"></a>Автоматизация с помощью Azure CLI

Чтобы реализовать автоматизацию с помощью Azure CLI, в среде необходимо установить интерфейс командной строки. Распространенные методы: 

* [Локальная установка Azure CLI.](/cli/azure/install-azure-cli)
* [Выполнение команд из контейнера Docker.](/cli/azure/run-azure-cli-docker)

## <a name="using-the-example-commands"></a>Использование примеров команд 

1. Замените переменные в скобках (`<...>`) собственными значениями. 
1. Значение репозитория GitHub для `<MY_GITHUB_DEFAULT_BRANCH_NAME>` зависит от используемого репозитория. В настоящее время типичными значениями являются `main` или `default`. Более старые репозитории могут использовать `master`. 

## <a name="log-in-for-automated-tasks-with-azure-cli"></a>Выполнение входа для выполнения автоматических задач с помощью Azure CLI

После установки Azure CLI необходимо выполнить вход, чтобы продолжить выполнение команд Azure CLI. Для реализации автоматизации можно пройти аутентификацию в Azure CLI.

**Справочная документация**: [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login)

Для аутентификации рекомендуется использовать [управляемое удостоверение](/cli/azure/authenticate-azure-cli#sign-in-with-a-managed-identity).

```azurecli
az login --identity
```

[Выполните вход с помощью субъекта-службы пользователя](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal) (после [создания такого субъекта-службы](../core/node-sdk-azure-authenticate-principal.md#create-a-service-principal-using-the-azure-cli-20)). 

```dotnetcli
read -sp "Azure password: " AZ_PASS && echo && \ 
    az login --service-principal \
    -u <MY-SP-APP-URL> \
    -p $AZ_PASS \
    --tenant <MY-TENANT>
```


[С учетными данными пользователя: имя пользователя и пароль](/cli/azure/authenticate-azure-cli#sign-in-with-credentials-on-the-command-line)

```dotnetcli
az login -u <MY_AZURE_USERNAME> -p <MY_AZURE_PASSWORD>
```    

## <a name="create-resource-group-for-resources"></a>Создание группы ресурсов для ресурсов

Группа ресурсов — это логическая коллекция ресурсов Azure. Логическое группирование основывается на службах, которые требуются для проекта в определенном регионе. Подробнее о [соглашениях об именовании](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming).

**Справочная документация**: [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create)

```azurecli
az group create \
    --name <MY-AZURE-RESOURCE_GROUP_NAME> \
    --location <AZURE_REGION_LOCATION>
```

## <a name="static-web-apps-with-azure-cli"></a>Использование статических веб-приложений с Azure CLI

Статическое веб-приложение содержит код для:

* внешнего приложения, содержащегося в репозитории GitHub;
* (необязательно) существующего API Функций Azure в каталоге `/API` ([подробнее](/azure/static-web-apps/add-api#create-the-api)).

Приложение может использовать функции Azure для бессерверных API, но это не обязательно для статических веб-приложений. 

**Справочная документация**: [az staticwebapp](/cli/azure/staticwebapp?view=azure-cli-latest)

### <a name="create-azure-static-web-app"></a>Создание статического веб-приложения Azure 

**Справочная документация**: [az staticwebapp create](/cli/azure/staticwebapp?view=azure-cli-latest#az_staticwebapp_create)

```azurecli
az staticwebapp create \
    --name <MY_AZURE_WEB_APP_NAME> \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME> \
    --source https://github.com/<MY_GITHUB_ACCOUNT_NAME>/<MY_AZURE_WEB_APP_NAME> \
    --location <AZURE_REGION_LOCATION> \
    --branch <MY_GITHUB_DEFAULT_BRANCH_NAME> \
    --app-artifact-location "<MY_WEB_APP_BUILD_DIRECTORY_NAME>" \
    --token <MY_GITHUB_PERSONAL_ACCESS_TOKEN>
```

### <a name="deploy-azure-static-web-app"></a>Развертывание статического веб-приложения Azure 

Чтобы развернуть приложение, с помощью Git отправьте его в удаленный репозиторий и набор ветвей во время создания ресурсов в предыдущем наборе. 

```bash
git push <REMOTE_NAME> <MY_GITHUB_DEFAULT_BRANCH_NAME>
```

Пример такой команды:

```bash
git push origin main
```

### <a name="delete-static-web-app"></a>Удаление статического веб-приложения 

**Справочная документация**: [az staticwebapp delete](/cli/azure/staticwebapp?view=azure-cli-latest#az_staticwebapp_delete)

```azurecli
az staticwebapp delete && \
    --name <MY_AZURE_WEB_APP_NAME> && \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. Создание и развертывание статического веб-приложения в Azure](../tutorial/static-web-app/introduction.md)
