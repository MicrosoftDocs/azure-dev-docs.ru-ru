---
title: Очистка ресурсов после развертывания контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство по Docker, часть 8. Очистка ресурсов
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 670579c82ff5b6d6b95fcd663ad2fa0e856805b9
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365077"
---
# <a name="part-8-clean-up-resources"></a>Часть 8. Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-docker-node-07.md)

Экземпляр Службы приложений, созданный для контейнера, включает резервный план этой службы, за использование которого может взиматься плата. Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выберите **Удалить**.

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

### <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-containers.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)
