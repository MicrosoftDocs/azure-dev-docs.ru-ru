---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a521396292214fd4e68b4625a0e5f5bcfca8be92
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672920"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Определение того, используются ли очереди или разделы Java Message Service (JMS)

Если приложение использует очереди или разделы JMS, их необходимо перенести на внешний сервер JMS. Использование Служебной шины Azure и Расширенного протокола управления очередью сообщений (AMQP) — это подходящая стратегия миграции для тех, кто работает с JMS. Сведения см. в руководстве по [использованию JMS со Служебной шиной Azure и AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Если постоянные хранилища JMS настроены, вам нужно записать их конфигурацию и применить ее после миграции.
