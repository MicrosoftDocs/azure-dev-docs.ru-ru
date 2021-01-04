---
title: Очистка ресурсов после развертывания контейнерного приложения Node.js с помощью Visual Studio Code
description: Руководство по Docker, часть 8. Очистка ресурсов
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 47f258ef2b6bb31e7b3a9ee47749c9b0bdd22aaf
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609304"
---
# <a name="part-8-clean-up-resources"></a>Часть 8. Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-docker-node-07.md)

## <a name="delete-app-service-resource-and-resource-group"></a>Удаление ресурса и группы ресурсов Службы приложений

Экземпляр Службы приложений, созданный для контейнера, включает резервный план этой службы, за использование которого может взиматься плата. Чтобы очистить эти ресурсы, щелкните правой кнопкой мыши Службу приложений в обозревателе **Azure: Служба приложений** и выберите **Удалить**.

Чтобы удалить эти ресурсы, можно посетить [портал Azure](https://portal.azure.com), щелкнуть **Группы ресурсов** в области навигации слева, выбрать созданную с помощью этого руководства группу ресурсов и щелкнуть **Удалить группу ресурсов**.

### <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](../../includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](../../how-to/deploy-containers.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)