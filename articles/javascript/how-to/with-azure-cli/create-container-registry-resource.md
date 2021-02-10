---
title: Создание пользовательского реестра контейнеров
description: Реестр контейнеров идеально подходит для образов контейнеров, которые вы хотите развернуть в Azure. Реестр позволяет управлять репозиториями и версиями контейнеров.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d60ea6d907a628d9b68c4c44ae72c1adca65ba29
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230176"
---
# <a name="create-and-use-container-registry"></a>Создание и использование реестра контейнеров

Реестр контейнеров идеально подходит для образов контейнеров, которые вы хотите развернуть в Azure.

Реестр позволяет управлять репозиториями и версиями контейнеров.  

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Создайте реестр с именем ресурса. Имя ресурса станет частью имени сервера входа для ресурса. 

Создайте реестр с помощью команды [az acr create](/cli/azure/acr#az_acr_create). 

```azurecli
az acr create \
    --resource-group YOUR-RESOURCE-GROUP
    --name YOUR-REGISTRY-NAME 
    --location westus 
    --admin-enabled
    --sku Basic
    --public-network-enabled false
```

## <a name="get-container-registry-credentials"></a>Получение учетных данных реестра контейнеров

Чтобы получить учетные данные, выполните команду [az acr credential show](/cli/azure/acr/credential#az_acr_credential_show) и запишите отображаемое имя пользователя и пароль:

```azurecli
az acr credential show \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-REGISTRY-NAME
```

## <a name="login-to-container-registry-with-docker-cli"></a>Вход в реестр контейнеров с помощью Docker CLI

С помощью учетных данных, созданных на предыдущем шаге, и отдельного имени входа сервера вы можете войти в реестр, используя стандартный рабочий процесс Docker CLI.

```bash
docker login YOUR-LOGIN_SERVER \
    --username USERNAME
    --password PASSWORD
```

## <a name="tag-your-local-image"></a>Добавление тега к локальному образу

Теперь вы можете отметить тегами свой контейнер Docker, чтобы определить связь с частным реестром. Для этого выполните следующую команду (замените `YOURALIAS/IMAGENAME` именем образа контейнера):

```bash
docker tag YOURALIAS/IMAGENAME \
    YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="push-your-local-image-to-your-container-registry"></a>Отправка локального образа в реестр контейнеров

```bash
docker push YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="configure-web-app-to-use-container"></a>Настройка веб-приложения для использования контейнера 

Настраивая веб-приложение Службы приложений для извлечения образа из реестра, выполните команду [az appservice web config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set):

```azurecli
az appservice web config container set \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-WEBAPP-NAME
    --docker-registry-server-url YOUR-LOGIN_SERVER \
    --docker-custom-image-name YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1 \
    -u USERNAME \
    -p PASSWORD
```

Обязательно добавьте префикс `https://` в начало параметра `--docker-registry-server-url`. Не добавляйте префикс к имени образа контейнера.

## <a name="next-steps"></a>Следующие шаги

* [Создание ресурса MongoDB CosmosDB](create-mongodb-cosmosdb.md)