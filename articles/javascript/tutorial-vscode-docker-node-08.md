---
title: Очистка ресурсов после развертывания контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство по Docker, часть 8. Очистка ресурсов
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: c50934c303b314b000ab5006a12f94009d417429
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405983"
---
# <a name="part-8-clean-up-resources"></a>Часть 8. Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-docker-node-07.md)

Экземпляр Службы приложений, созданный для контейнера, включает резервный план этой службы, за использование которого может взиматься плата. Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выберите **Удалить**.

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

### <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](./how-to/deploy-containers.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)