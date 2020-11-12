---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c8cf3e2f478265a81742093315e2a4041b2f0ed9
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405205"
---
Разверните код в локальной папке ( *python-docs-hello-world* ) с помощью команды `az webapp up`.

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Если команда `az` не распознана, проверьте, установили ли вы Azure CLI согласно сведениям, указанным в разделе [Настройка начальной среды](../../quickstart-python-flask-multipage.yml?tutorial-step=1).
- Если команда `webapp` не распознается, убедитесь, что вы используете Azure CLI версии 2.0.80 или более поздней. Если нет, [установите последнюю версию](/cli/azure/install-azure-cli).
- Замените `<app_name>` именем, уникальным для всех регионов Azure ( *допустимыми символами являются `a-z`, `0-9`и `-`* ). Рекомендуется использовать сочетание названия компании и идентификатора приложения.
- Аргумент `--sku F1` создает веб-приложение в ценовой категории "Бесплатный". Этот аргумент можно опустить, чтобы использовать более быструю ценовую категорию "Премиум" с почасовой оплатой.
- При необходимости вы можете использовать аргумент `--location <location-name>`, где `<location_name>` является доступным регионом Azure. Список допустимых регионов для учетной записи Azure можно получить, выполнив команду [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).
- Если отображается сообщение об ошибке Could not auto-detect the runtime stack of your app (Не удалось автоматически определить стек среды выполнения приложения), убедитесь, что вы выполняете команду в папке *python-docs-hello-world*  (Flask) или в папке *python-docs-hello-django*  (Django), где находится файл *requirements.txt*. (см. статью об [устранение неполадок с автоматическим обнаружением с помощью команды az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) на сайте GitHub).

Выполнение этой команды может занять несколько минут. Во время выполнения она предоставляет сообщения о создании группы ресурсов, плане службы приложений и приложении размещения, настройке ведения журнала и последующем выполнении развертывания ZIP-файла. Затем оно выдает сообщение You can launch the app at http://&lt;app-name&gt;.azurewebsites.net (Вы можете запустить приложение по адресу http://<app-name>.azurewebsites.net). Это URL-адрес приложения в Azure.

![Пример выходных данных команды az webapp up](../../media/quickstart-python/az-webapp-up-output.png)

[!include [az webapp up command note](../app-service-web-az-webapp-up-note.md)]