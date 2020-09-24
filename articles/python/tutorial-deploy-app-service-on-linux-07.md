---
title: Шаг 7. Очистка ресурсов после развертывания в Службе приложений Azure в Linux с помощью Visual Studio Code
description: Шаг руководства 7. Очистка ресурсов Azure
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: cc1acdd316406d9269c97a4f5b25f465d0303509
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831480"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Очистка ресурсов после развертывания в Службе приложений Azure в Linux с помощью Visual Studio Code

[Предыдущий шаг: потоковая передача журналов](tutorial-deploy-app-service-on-linux-06.md)

Созданная Служба приложений Azure включает резервный план службы приложений, за использование которого может взиматься плата. Чтобы избежать затрат, удалите группу ресурсов, содержащую все эти ресурсы.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

Поздравляем с завершением пошагового руководства по развертыванию кода Python в Службе приложений Azure в Linux.

Как мы уже упоминали, вы можете узнать о расширении Службы приложений в репозитории [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) на сайте GitHub. Мы будем рады любым предложениям и сообщениям о проблемах.

См. сведения о службах Azure, которые можно использовать с Python, в том числе о хранилище данных, ИИ и Машинном обучении, в [центре разработчиков Azure на Python](/python/azure/?view=azure-python).

Для VS Code существуют и другие расширения Azure, которые могут оказаться полезными. Выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для Visual Studio Code](media/deploy-containers/azure-extensions-for-visual-studio-code.png)

Некоторые популярные расширения:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager (ARM)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Все готово](/python/azure/?view=azure-python) 

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskVSCQuickstartHelp)