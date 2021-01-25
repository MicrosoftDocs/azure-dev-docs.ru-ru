---
title: Создание Службы приложений Azure для размещения приложений с помощью Azure CLI
description: Руководство, часть 3. Создание Службы приложений с помощью Azure CLI
ms.topic: tutorial
ms.date: 01/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ad8e0836d62102521b557fd45b24291d52db447c
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561520"
---
# <a name="create-the-app-service"></a>Создание службы приложений

[Предыдущий шаг. Создание приложения](tutorial-vscode-azure-cli-node-02.md)

На этом этапе вы создадите Службу приложений Azure, где будет размещаться код приложения, с помощью Azure CLI.

<a name="create-resource-group"></a>

## <a name="create-resource-group-and-set-as-default-value"></a>Создание группу ресурсов и ее определение в качестве значения по умолчанию

1. В окне терминала или командной строки выполните следующую команду, чтобы создать **группу ресурсов** для Службы приложений. По сути, группа ресурсов — это именованная коллекция ресурсов Azure для приложения, таких как веб-сайт, база данных, Функции Azure и т. п.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    Команда Azure CLI [`az group create`](/cli/azure/group#az_group_create) выше создает группу ресурсов с именем `myResourceGroup` в центре обработки данных `westus`. Эти значения можно изменить.

    Когда выполнение команды успешно завершится, отобразятся выходные данные в формате JSON со сведениями о новой группе ресурсов.

1. Выполните следующую команду Azure CLI, [`az configure`](/cli/azure/config), чтобы настроить группу ресурсов и регион по умолчанию для последующих команд. Такое действие избавляет от необходимости постоянно указывать эти значения (при успешном выполнении эта команда не возвращает никаких данных).

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

## <a name="create-and-deploy-web-app-service-with-azure-cli-command"></a>Создание и развертывание службы веб-приложений с помощью команды Azure CLI

Выполните следующую команду Azure CLI ([`az webapp up`](/cli/azure/webapp#az_webapp_up)), чтобы создать и развернуть приложение Службы приложений. Замените `<your_app_name>` уникальным именем, которое стало URL-адресом `http://<your_app_name>.azurewebsites.net`. 

```azurecli
az webapp up --name <your_app_name> --logs --launch-browser
```

Команда `--logs` отображает поток журнала сразу после запуска веб-приложения. Команда `--launch-browser` открывает браузер по умолчанию для нового приложения. Эту же команду можно использовать для повторного развертывания всего приложения. 

## <a name="troubleshooting"></a>Устранение неполадок

* Если вы получили ошибку об отсутствующем обязательном параметре `--resource-group`, вернитесь к началу статьи и задайте значения по умолчанию или укажите параметр и значение. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные команды для веб-приложения можно получить с помощью группы команд Azure [webapp](/cli/azure/webapp) или группы команд [Службы приложений Azure](/cli/azure/appservice). 

> [!div class="nextstepaction"]
> [Служба приложений создана](tutorial-vscode-azure-cli-node-04.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
