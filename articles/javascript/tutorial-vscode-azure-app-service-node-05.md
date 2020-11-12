---
title: Очистка ресурсов после развертывания в Службе приложений Azure с помощью Visual Studio Code
description: Руководство по Node.js, часть 5. Очистка ресурсов
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 19f94218059da688aed9fb806427ae8179b78baa
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405923"
---
# <a name="part-5-clean-up-resources"></a>Часть 5. Очистка ресурсов

[Предыдущий шаг. Потоковая передача журналов](tutorial-vscode-azure-app-service-node-04.md)

Созданная Служба приложений включает резервный план Службы приложений на основе ценовой категории "Бесплатный". При использовании этого плана плата не взимается.

Чтобы удалить эти ресурсы, перейдите на [портал Azure](https://portal.azure.com), выберите **Группы ресурсов** , найдите и выберите группу ресурсов (например, `AppServiceTutorial-rg`), созданную при работе с этим руководством, и используйте команду **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](./how-to/deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=clean-up-resources)