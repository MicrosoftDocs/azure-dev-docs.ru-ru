---
title: Руководство. Очистка ресурсов, используемых с кодом Python, в Функциях Azure
description: 'Шаг руководства 8: очистка ресурсов Azure, позволяющая избежать постоянных расходов.'
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seo-python-october2019
ms.openlocfilehash: 264c09a8d84c7115bb0a56d0455d576187695db0
ms.sourcegitcommit: a8073315f751631ab983618fa9f812eb95d8b2dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125257"
---
# <a name="tutorial-clean-up-azure-resources-for-azure-functions"></a>Руководство. Очистка ресурсов Azure для Функций Azure

[Предыдущий шаг: добавление привязки к хранилищу](tutorial-vs-code-serverless-python-07.md)

В этой статье показано, как удалить ресурсы Azure, созданные при работе с этим руководством. Приложение-функция Azure, созданная с помощью Visual Studio Code, включает ресурсы, за использование которых может взиматься минимальная плата. См. сведения на странице с [ценами на Функции](https://azure.microsoft.com/pricing/details/functions/).

Лучший способ очистки — удалить группу ресурсов, содержащую все ресурсы, используемые в этом руководстве. К ресурсам относятся приложение-функция, учетная запись хранения и резервный план службы приложений.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

В Visual Studio Code видно, что контекстное меню в приложении-функция в обозревателе **Azure: Функции** включает команду **Удалить приложение-функцию**. Но эта команда удаляет только приложение-функцию, оставляя другие ресурсы, использование которых может повлечь затраты.

## <a name="next-steps"></a>Дальнейшие действия

Поздравляем с завершением пошагового руководства по развертыванию кода Python в службе "Функции Azure". Теперь вы готовы создавать много новых бессерверных функций.

Как мыуже упоминали, вы можете получить дополнительные сведения о расширении Функций в репозитории GitHub [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Мы будем рады любым предложениям и сообщениям о проблемах.

Изучите [обзор Функций Azure](/azure/azure-functions/functions-overview), чтобы ознакомиться с доступными для использования триггерами.

См. сведения о службах Azure, которые можно использовать с Python, в том числе о хранилище данных, ИИ и Машинном обучении, в [центре разработчиков Azure на Python](/azure/python/?view=azure-python).

Для Visual Studio Code существуют и другие расширения Azure, которые могут оказаться полезными. Выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions-for-visual-studio-code.png)

Некоторые популярные расширения:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Служба приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Дополнительные сведения [о развертывании в Службе приложений](tutorial-deploy-app-service-on-linux-01.md).
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Все готово](https://docs.microsoft.com/python/azure/?view=azure-python)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=08-clean-up-resources)
