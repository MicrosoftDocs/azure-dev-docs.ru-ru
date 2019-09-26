---
title: Очистка ресурсов Azure
description: 'Шаг руководства 8: очистка ресурсов Azure, позволяющая избежать постоянных расходов.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 6996121fc8ecba4489e2ec920de35574f6d1c5d8
ms.sourcegitcommit: d6575ac86449380b5a9c6c66aa722cb33ed53438
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186177"
---
# <a name="clean-up-resources"></a>Очистка ресурсов

[Предыдущий шаг: добавление привязки к хранилищу](tutorial-vs-code-serverless-python-07.md)

Приложение-функция, которое вы создали, содержит ресурсы с минимальной стоимостью (см.[цены на Функции](https://azure.microsoft.com/pricing/details/functions/)). Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши приложение-функцию в обозревателе **Azure: Функции** и выберите действие **Delete Function App** (Удалить приложение-функцию).

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Поздравляем с завершением пошагового руководства по развертыванию кода Python в службе "Функции Azure". Теперь вы готовы создавать много новых бессерверных функций.

Как мыуже упоминали, вы можете получить дополнительные сведения о расширении Функций в репозитории GitHub [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Мы будем рады любым предложениям и сообщениям о проблемах.

Изучите [обзор Функций Azure](/azure/azure-functions/functions-overview), чтобы ознакомиться с доступными для использования триггерами.

См. сведения о службах Azure, которые можно использовать с Python, в том числе о хранилище данных, ИИ и Машинном обучении, в [центре разработчиков Azure на Python](/azure/python/?view=azure-python).

Для Visual Studio Code существуют и другие расширения Azure, которые могут оказаться полезными. Выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Некоторые популярные расширения:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Служба приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Дополнительные сведения [о развертывании в Службе приложений](tutorial-deploy-app-service-on-linux-01.md).
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Все готово](https://docs.microsoft.com/python/azure/?view=azure-python)

[У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=08-clean-up-resources)
