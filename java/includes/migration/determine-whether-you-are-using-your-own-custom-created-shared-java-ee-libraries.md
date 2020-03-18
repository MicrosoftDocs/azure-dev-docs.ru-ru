---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 64a4a0e191c70d930ad8c5f9d19cb81dfb9cb851
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894124"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Определение того, используются ли настраиваемые общие библиотеки Java EE

Если вы используете общие библиотеки Java EE, у вас есть два варианта:

* Выполните рефакторинг кода приложения, чтобы удалить все зависимости от библиотек и внедрить функции непосредственно в приложение.
* Включите библиотеки в путь к классу сервера.
