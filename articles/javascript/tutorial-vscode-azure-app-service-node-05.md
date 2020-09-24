---
title: Очистка ресурсов после развертывания в Службе приложений Azure с помощью Visual Studio Code
description: Руководство, часть 5. Очистка ресурсов.
ms.topic: conceptual
ms.date: 03/04/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: fc420d98121816157e75017ee453aa42929f68e6
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "90772897"
---
# <a name="part-5-clean-up-resources"></a>Часть 5. Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-azure-app-service-node-04.md)

Созданная Служба приложений включает резервный план Службы приложений на основе ценовой категории "Бесплатный". При использовании этого плана плата не взимается.

Чтобы удалить эти ресурсы, перейдите на [портал Azure](https://portal.azure.com), выберите **Группы ресурсов**, найдите и выберите группу ресурсов (например, `AppServiceTutorial-rg`), созданную при работе с этим руководством, и используйте команду **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=clean-up-resources)
