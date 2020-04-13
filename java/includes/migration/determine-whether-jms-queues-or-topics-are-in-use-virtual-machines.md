---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 69bddfce67388d3392e6908f3ddf1af378b116cf
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624085"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Определение того, используются ли очереди или разделы Java Message Service (JMS)

Если приложение использует очереди или разделы JMS, их необходимо перенести на внешний размещенный сервер JMS. Использование Служебной шины Azure и Расширенного протокола управления очередью сообщений — это подходящая стратегия миграции для тех, кто работает с JMS. Сведения см. в руководстве по [использованию JMS со Служебной шиной Azure и AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Если постоянные хранилища JMS настроены, вам нужно записать их конфигурацию и применить ее после миграции.

Если вы используете Oracle Message Broker, можете перенести это программное обеспечение на виртуальные машины Azure и использовать его как есть.
