---
title: Просмотр журналов на портале Azure
description: Узнайте, как просмотреть журналы, созданные Azure Monitor и Application Insights.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd0741cd58e8d2e882a352035cd3c4f9bca956d1
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625073"
---
# <a name="6-view-logs-in-azure-portal"></a>6. Просмотр журналов на портале Azure

Из этого раздела руководства вы узнаете, как просмотреть журналы, созданные Azure Monitor и Application Insights. 

## <a name="count-of-traces-in-logs-with-azure-cli"></a>Число трассировок в журналах с помощью Azure CLI

Используйте Azure CLI для просмотра важных сведений в журналах. Например, используйте следующую команду, чтобы просмотреть, сколько трассировок зарегистрировано в журналах. 

Помните, что трассировка добавлена только для маршрута `/trace`. Вызовы к корню веб-приложения не приводят к регистрации трассировок. 

```azurecli
az monitor app-insights metrics show \
    --resource-group rg-demo-vm-eastus \
    --app demoWebAppMonitor \
    --metric traces/count
```

Ответ будет похож на приведенный ниже. В этом примере зарегистрировано всего две трассировки: 

```console
{
  "value": {
    "end": "2020-11-11T21:33:40.311000+00:00",
    "interval": null,
    "segments": null,
    "start": "2020-11-11T20:33:40.311000+00:00",
    "traces/count": {
      "sum": 2
    }
  }
}
```

## <a name="view-application-traces-in-azure-portal"></a>Просмотр трассировок приложений на портале Azure

Если вы хотите просмотреть трассировки в виде списка, легче всего это сделать на портале Azure. 

1. Откройте [портал Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll) в веб-браузере.
1. Отфильтруйте список ресурсов по группе ресурсов `rg-demo-vm-eastus`. 
1. Выберите ресурс `demoWebAppMonitor`. 
1. Перейдите в раздел **Мониторинг** и выберите **Журналы**. Если отобразится всплывающее окно с запросами, щелкните значок **X** в углу, чтобы закрыть его.
1. Выберите объект **Application Insights** с именем **traces**, дважды щелкнув его. Его имя будет добавлено в окно запросов. 
1. Выполните запрос, нажав кнопку **Выполнить**.
1. Настраиваемые трассировки Azure Monitor Application Insights из веб-приложения отобразятся в списке.

    :::image type="content" source="../../media/tutorial-vm/azure-portal-application-insights-custom-trace.png" alt-text="Настраиваемые трассировки Azure Monitor Application Insights из веб-приложения отображаются в списке.":::

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Очистка ресурсов Azure](clean-up-resources.md) 