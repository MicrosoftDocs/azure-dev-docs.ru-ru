---
title: Создание ресурса Azure Monitor
description: Узнайте, как создать группу ресурсов Azure для всех ваших ресурсов Azure и ресурс Monitor для сбора файлов журналов веб-приложения в облаке Azure. Azure Monitor — это служба Azure, а Application Insights — клиентская библиотека, используемая при работе с руководством.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b1b634756a5188cbd9233274205005f62b25026
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561050"
---
# <a name="2-create-application-insights-resource-for-web-pages"></a>2. Создание ресурса Application Insights для веб-страниц

На этом шаге руководства вы создадите группу ресурсов Azure для всех ваших ресурсов Azure и ресурс Monitor для сбора файлов журналов веб-приложения в облаке Azure. Azure Monitor — это служба Azure, а Application Insights — клиентская библиотека, используемая при работе с руководством. 

## <a name="create-a-resource-group-for-your-virtual-machine-resources"></a>Создание группы ресурсов для ресурсов виртуальной машины

В этом руководстве используется несколько ресурсов Azure. Создание группы ресурсов позволяет вам с легкостью находить ресурсы и удалять их, когда они больше не нужны.

1. В терминале или оболочке Bash введите [команду Azure CLI для создания группы ресурсов Azure](/cli/azure/group#az_group_create) с именем `rg-demo-vm-eastus`:

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo-vm-eastus 
    ```

## <a name="create-azure-monitor-resource-with-azure-cli"></a>Создание ресурса Azure Monitor с помощью Azure CLI

1. Установите расширение Application Insights в Azure CLI.

    ```azurecli
    az extension add -n application-insights
    ```

1. Выполните следующую команду, чтобы [создать ресурс мониторинга](/cli/azure/ext/application-insights/monitor/app-insights/component#ext_application_insights_az_monitor_app_insights_component_create):


    ```azurecli
    az monitor app-insights component create \
      --app demoWebAppMonitor \
      --location eastus \
      --resource-group rg-demo-vm-eastus
    ```

    В результатах найдите и скопируйте ключ `instrumentationKey`. Позже он вам понадобится. 

1. Оставьте терминал открытым, так как он понадобится на следующем шаге.

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Создание виртуальной машины Linux](create-linux-virtual-machine-azure-cli.md). 
