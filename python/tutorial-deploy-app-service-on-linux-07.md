---
title: Руководство по Очистка ресурсов после развертывания в Службе приложений Azure в Linux с помощью Visual Studio Code
description: Шаг руководства 7. Очистка ресурсов Azure
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 4ffadce6a6895041efe6737b271d7ab11c830095
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172470"
---
# <a name="tutorial-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>Руководство по Очистка ресурсов после развертывания в Службе приложений Azure в Linux с помощью Visual Studio Code

[Предыдущий шаг: потоковая передача журналов](tutorial-deploy-app-service-on-linux-06.md)

Созданная Служба приложений включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выберите **Удалить**.

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Поздравляем с завершением пошагового руководства по развертыванию кода Python в Службе приложений Azure в Linux.

Как мы уже упоминали, вы можете узнать о расширении Службы приложений в репозитории [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) на сайте GitHub. Мы будем рады любым предложениям и сообщениям о проблемах.

См. сведения о службах Azure, которые можно использовать с Python, в том числе о хранилище данных, ИИ и Машинном обучении, в [центре разработчиков Azure на Python](https://docs.microsoft.com/python/azure/?view=azure-python).

Для VS Code существуют и другие расширения Azure, которые могут оказаться полезными. Выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для VS Code](media/deploy-containers/azure-extensions.png)

Некоторые популярные расширения:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager (ARM)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Все готово](https://docs.microsoft.com/python/azure/?view=azure-python) 

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=07-clean-up-resources)
