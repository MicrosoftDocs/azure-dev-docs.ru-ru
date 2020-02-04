---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 2dccfba5cfe61ac862aeabe7b928e121502093b7
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830882"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Определение того, используются ли настраиваемые общие библиотеки Java EE

Если вы используете общие библиотеки Java EE, у вас есть два варианта:

1. Выполните рефакторинг кода приложения, чтобы удалить все зависимости от библиотек и внедрить функции непосредственно в приложение.
2. Включите библиотеки в путь к классу сервера.
