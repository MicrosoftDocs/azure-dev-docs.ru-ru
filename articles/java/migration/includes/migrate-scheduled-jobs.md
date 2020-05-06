---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 1015c179c0f93485decd77bd89a3ceec8833652e
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672160"
---
### <a name="migrate-scheduled-jobs"></a>Перенос запланированных заданий

Для выполнения запланированных заданий в Azure рекомендуется использовать [триггер таймера для Функций Azure](/azure/azure-functions/functions-bindings-timer). Переносить сам код задания в функцию не нужно. Функция может просто вызвать URL-адрес в приложении, чтобы активировать задание. Если такие выполнения заданий должны быть динамически вызываемыми и (или) централизованно отслеживаемыми, попробуйте использовать [Spring Batch](https://spring.io/projects/spring-batch).

Либо создайте приложение логики с триггером повторения для вызова URL-адреса без необходимости писать код за пределами приложения. См. сведения об [Azure Logic Apps](/azure/logic-apps/logic-apps-overview), а также руководство по [созданию, планированию и выполнению повторяющихся задач и рабочих процессов с помощью триггера повторения в Azure Logic Apps](/azure/connectors/connectors-native-recurrence).

> [!NOTE]
> Чтобы предотвратить вредоносное использование, необходимо убедиться, что конечная точка вызова задания запрашивает ввод учетных данных. В этом случае функция для триггеров должна предоставить учетные данные.