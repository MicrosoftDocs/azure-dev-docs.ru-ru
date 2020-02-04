---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: e36fae7adfda8c05e222151872b137fa600cdd97
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830772"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Определение того, используются ли очереди или разделы JMS

Если приложение использует очереди или разделы JMS, их необходимо перенести на внешний размещенный сервер JMS. Использование Служебной шины Azure и Расширенного протокола управления очередью сообщений — это подходящая стратегия миграции для тех, кто работает с JMS. См. сведения в руководстве по [использованию JMS со Служебной шиной Azure и AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Если постоянные хранилища JMS настроены, вам нужно записать их конфигурацию и применить ее после миграции.

Если вы используете Oracle Message Broker, вы можете перенести это программное обеспечение на виртуальные машины Azure и использовать его как есть.
