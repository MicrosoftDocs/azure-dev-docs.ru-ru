---
title: Руководство по Очистка ресурсов Azure. Функции Azure на Python
description: 'Шаг руководства 8: очистка ресурсов Azure, позволяющая избежать постоянных расходов.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: ddc2ab44d3d8865c89cb6cdf8368461b6e4f666a
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465945"
---
# <a name="tutorial-clean-up-azure-resources-for-azure-functions"></a>Руководство по Очистка ресурсов Azure для Функций Azure

[Предыдущий шаг: добавление привязки к хранилищу](tutorial-vs-code-serverless-python-07.md)

В этой статье показано, как удалить ресурсы Azure, созданные при работе с этим руководством. Приложение-функция Azure, созданная с помощью Visual Studio Code, включает ресурсы, за использование которых может взиматься минимальная плата.

Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши приложение-функцию в обозревателе **Azure: Функции** и выберите действие **Delete Function App** (Удалить приложение-функцию). Дополнительные сведения см. на [странице с ценами на Функции](https://azure.microsoft.com/pricing/details/functions/).

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

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
