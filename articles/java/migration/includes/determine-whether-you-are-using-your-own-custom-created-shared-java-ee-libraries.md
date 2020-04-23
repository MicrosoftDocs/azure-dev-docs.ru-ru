---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 64a4a0e191c70d930ad8c5f9d19cb81dfb9cb851
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673420"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Определение того, используются ли настраиваемые общие библиотеки Java EE

Если вы используете общие библиотеки Java EE, у вас есть два варианта:

* Выполните рефакторинг кода приложения, чтобы удалить все зависимости от библиотек и внедрить функции непосредственно в приложение.
* Включите библиотеки в путь к классу сервера.
