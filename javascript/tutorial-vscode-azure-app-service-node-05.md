---
title: Очистка ресурсов после развертывания в Службе приложений Azure с помощью Visual Studio Code
description: Руководство, часть 5. Очистка ресурсов.
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: 1c3b277270bf6f8f7cb5884b7bb56ce2c9b3368c
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "78894213"
---
# <a name="clean-up-resources"></a>Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-azure-app-service-node-04.md)

Созданная Служба приложений включает резервный план Службы приложений на основе ценовой категории "Бесплатный". При использовании этого плана плата не взимается.

Чтобы удалить эти ресурсы, перейдите на [портал Azure](https://portal.azure.com), выберите **Группы ресурсов**, найдите и выберите группу ресурсов (например, `AppServiceTutorial-rg`), созданную при работе с этим руководством, и используйте команду **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=clean-up-resources)
