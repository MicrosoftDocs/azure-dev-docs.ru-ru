---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f5995a3c5efc46bc58b446589ce089bf7d86b2ba
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673080"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Определение того, использует ли приложение адаптер ресурсов

Если приложению требуется адаптер ресурсов, он должен быть совместим с WildFly. Определите, хорошо ли работает адаптер ресурсов в отдельном экземпляре WildFly, развернув его на сервере и правильно настроив. Если адаптер ресурсов работает правильно, добавьте JAR в путь к классу сервера образа Docker и поместите необходимые файлы конфигурации в нужное расположение в каталогах сервера WildFly, чтобы обеспечить доступность.
