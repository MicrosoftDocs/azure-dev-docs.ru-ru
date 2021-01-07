---
title: Шаг 8. Очистка ресурсов, используемых с бессерверным кодом Python, в Функциях Azure
description: Шаг учебника 8. Очистка ресурсов Azure, позволяющая избежать постоянных расходов.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperf-fy21q2
ms.openlocfilehash: 499d738e9f979a249b421287644b089530e5ef27
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911434"
---
# <a name="8-clean-up-azure-resources-for-azure-functions"></a>8: Очистка ресурсов Azure для Функций Azure

[Предыдущий шаг: добавление привязки к хранилищу](tutorial-vs-code-serverless-python-07.md)

Приложение Функций Azure, созданное с помощью Visual Studio Code в ходе работы с этим учебником, включает ресурсы, за использование которых может взиматься минимальная плата. См. сведения на странице с [ценами на Функции](https://azure.microsoft.com/pricing/details/functions/).

Лучший способ очистки — удалить группу ресурсов, содержащую все ресурсы, используемые в этом руководстве. К ресурсам относятся приложение-функция, учетная запись хранения и резервный план службы приложений.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

В Visual Studio Code видно, что контекстное меню в приложении-функция в обозревателе **Azure: Функции** включает команду **Удалить приложение-функцию**. Но эта команда удаляет только приложение-функцию, оставляя другие ресурсы, использование которых может повлечь затраты.

## <a name="next-steps"></a>Дальнейшие действия

Поздравляем с завершением пошагового руководства по развертыванию кода Python в службе "Функции Azure". Теперь вы готовы создавать много новых бессерверных функций.

Как мыуже упоминали, вы можете получить дополнительные сведения о расширении Функций в репозитории GitHub [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Мы будем рады любым предложениям и сообщениям о проблемах.

Изучите [обзор Функций Azure](/azure/azure-functions/functions-overview), чтобы ознакомиться с доступными для использования триггерами.

См. сведения о службах Azure, которые можно использовать с Python, в том числе о хранилище данных, ИИ и Машинном обучении, в [центре разработчиков Azure на Python](./index.yml).

Для Visual Studio Code существуют и другие расширения Azure, которые могут оказаться полезными. Выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions-for-visual-studio-code.png)

Некоторые популярные расширения:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Служба приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Дополнительные сведения [о развертывании в Службе приложений](tutorial-deploy-app-service-on-linux-01.md).
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Все готово](/python/azure/?preserve-view=true&view=azure-python)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
