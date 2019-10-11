---
title: Очистка ресурсов после развертывания контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство, часть 6. Очистка ресурсов.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 53fbfc2c3dc04ed30e940a680eb4c65de6591504
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686011"
---
# <a name="clean-up-resources"></a>Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-docker-node-05.md)

Экземпляр Службы приложений, созданный для контейнера, включает резервный план этой службы, за использование которого может взиматься плата. Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выберите **Удалить**.

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-containers.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)
