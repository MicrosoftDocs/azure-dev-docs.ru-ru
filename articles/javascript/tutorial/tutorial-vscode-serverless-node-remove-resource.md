---
title: Удаление дорогостоящих ресурсов Azure для удаленной работы после развертывания приложения в решении "Функции Azure"
description: Удалите (очистите) ресурсы Azure для удаленной работы, чтобы на них не расходовались средства. Чтобы очистить ресурсы, щелкните правой кнопкой мыши приложение-функцию в обозревателе Функций Azure и выберите элемент **Удалить приложение-функцию**.
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: devx-track-js, contperfq2
ms.openlocfilehash: 7d2a0b73a831535a006808973c1a021ef9dec343
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338512"
---
# <a name="5-clean-up-azure-resources-for-azure-functions-tutorial"></a>5. Руководство по очистке ресурсов Azure для Функций Azure

[Предыдущий шаг. Развертывание приложения в решении "Функции"](tutorial-vscode-serverless-node-deploy-hosting.md)

## <a name="remove-remote-azure-resources"></a>Удаление ресурсов Azure для удаленной работы

Приложение Функций, которое вы создали, содержит ресурсы с минимальной стоимостью (см.[цены на Функции](https://azure.microsoft.com/pricing/details/functions/)). Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши приложение-функцию в обозревателе **Azure: Функции** и выберите действие **Delete Function App** (Удалить приложение-функцию).

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

[!INCLUDE [Next steps for using VSCode extensions](../includes/tutorial-next-steps-vscode-extensions.md)]

[!INCLUDE [Next steps for using JavaScript on Azure](../includes/tutorial-next-steps-js-azure.md)]

## <a name="learn-more-about-azure-functions"></a>Дополнительные сведения о решении "Функции Azure"

* [Руководство разработчиков Функций Azure](/azure/azure-functions/functions-reference)
* [Руководство разработчика JavaScript для Функций Azure](/azure/azure-functions/functions-reference-node)
* [Защита Функций Azure](/azure/azure-functions/security-concepts)
* Рекомендации по [хранению](/azure/azure-functions/storage-considerations) и [производительности](/azure/azure-functions/functions-best-practices)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Готово](../how-to/develop-serverless-apps.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=clean-up-resources)