---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 64a4a0e191c70d930ad8c5f9d19cb81dfb9cb851
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673420"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Определение того, используются ли настраиваемые общие библиотеки Java EE

Если вы используете общие библиотеки Java EE, у вас есть два варианта:

* Выполните рефакторинг кода приложения, чтобы удалить все зависимости от библиотек и внедрить функции непосредственно в приложение.
* Включите библиотеки в путь к классу сервера.
