---
title: Создание Службы приложений Azure для размещения приложений с помощью Azure CLI
description: Руководство, часть 3. Создание Службы приложений с помощью Azure CLI
ms.topic: tutorial
ms.date: 12/18/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 24b64c5b618d06083ddf5eee3be7c6e53923a99d
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911404"
---
# <a name="create-the-app-service"></a>Создание службы приложений

[Предыдущий шаг. Создание приложения](tutorial-vscode-azure-cli-node-02.md)

На этом этапе вы создадите Службу приложений Azure, где будет размещаться код приложения, с помощью Azure CLI.

## <a name="create-resource-group"></a>Создать группу ресурсов

1. В окне терминала или командной строки выполните следующую команду, чтобы создать **группу ресурсов** для Службы приложений. По сути, группа ресурсов — это именованная коллекция ресурсов Azure для приложения, таких как веб-сайт, база данных, Функции Azure и т. п.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    Команда Azure CLI [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) выше создает группу ресурсов с именем `myResourceGroup` в центре обработки данных `westus`. Эти значения можно изменить.

    Когда выполнение команды успешно завершится, отобразятся выходные данные в формате JSON со сведениями о новой группе ресурсов.

1. Выполните следующую команду Azure CLI, [`az configure`](/cli/azure/config?view=azure-cli-latest), чтобы настроить группу ресурсов и регион по умолчанию для последующих команд. Такое действие избавляет от необходимости постоянно указывать эти значения (при успешном выполнении эта команда не возвращает никаких данных).

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

## <a name="create-app-service-plan"></a>Создание плана службы приложений

Выполните следующую команду Azure CLI, [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create), чтобы создать **план службы приложений**, который определяет базовую виртуальную машину для Службы приложений:

```azurecli
az appservice plan create --name myPlan --sku F1
```

С помощью представленной выше команды определяется [план размещения бесплатной ценовой категории](../../core/what-is-azure-for-javascript-development.md#free-tier-resources) (`--sku F1`), для которого используется общая виртуальная машина. Затем этому плану присваивается имя `myPlan`. 

## <a name="create-web-app-service"></a>Создание службы веб-приложений

Выполните следующую команду Azure CLI, [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create), чтобы создать Службу приложений, заменив значение `<your_app_name>` уникальным именем, которое станет URL-адресом (`http://<your_app_name>.azurewebsites.net`), с помощью [последней версии среды выполнения Node.js](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes&preserve-view=false). 

```azurecli
az webapp create --name <your_app_name> --plan myPlan -g --runtime "node|12-lts"
```

## <a name="browse-web-app"></a>Просмотр веб-приложения

1. Выполните следующую команду Azure CLI, [`az webapp browse`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_browse), чтобы открыть браузер в новой Службе приложений, заменив значение `<your_app_name>` реальным именем:

    ```azurecli
    az webapp browse --name <your_app_name>
    ```

1. Вы еще не развернули пользовательский код для приложения. Поэтому в браузере отобразится страница по умолчанию:

    ![Страница службы приложений по умолчанию](../../media/azure-cli/azure-default-page.png)

## <a name="troubleshooting"></a>Устранение неполадок

* Если вы получили ошибку об отсутствующем обязательном параметре `--resource-group`, вернитесь к началу статьи и задайте значения по умолчанию или укажите параметр и значение. 

> [!div class="nextstepaction"]
> [Служба приложений создана](tutorial-vscode-azure-cli-node-04.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
