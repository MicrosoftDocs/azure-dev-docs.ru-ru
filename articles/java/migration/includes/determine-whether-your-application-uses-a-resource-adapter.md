---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 9fa87bf3d0de64271a9ffb0e7e8fbff3cd1afd12
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738498"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Определение того, использует ли приложение адаптер ресурсов

Если приложению требуется адаптер ресурсов, он должен быть совместим с WildFly. Определите, хорошо ли работает адаптер ресурсов в отдельном экземпляре WildFly, развернув его на сервере и правильно настроив. Если адаптер ресурсов работает правильно, добавьте JAR в путь к классу сервера образа Docker и поместите необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly, чтобы обеспечить доступность.
