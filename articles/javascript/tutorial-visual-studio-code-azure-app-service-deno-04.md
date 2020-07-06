---
title: Очистка ресурсов после развертывания в Службе приложений Azure
description: Руководство, часть 4. Очистка ресурсов
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 1f452b054aa41c714105e79bd3dc971c88acd260
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792474"
---
# <a name="clean-up"></a>Очистка

[Предыдущий шаг. Развертывание приложения](tutorial-visual-studio-code-azure-app-service-deno-03.md)

С помощью инструкций из этого раздела мы удалим и очистим все созданные ресурсы.

## <a name="remove-your-resources"></a>Удаление ресурсов

Созданная Служба приложений включает резервный план Службы приложений на основе ценовой категории "Бесплатный". При использовании этого плана плата не взимается.

Чтобы очистить ресурсы, перейдите на [портал Azure](https://portal.azure.com), выберите **Группы ресурсов**, найдите и выберите группу ресурсов (например, `deno-quickstart`), созданную при работе с этим руководством, и используйте команду **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Готово](node-howto-deploy-web-app.md) [Возникла проблема](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
