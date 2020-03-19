---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: b2458a80eccfcae24a3364208334cce3aedea861
ms.sourcegitcommit: 21ddeb9bd9abd419d143dc2ca8a7c821a1758cf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129405"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Определение того, используются ли очереди или разделы JMS

Если приложение использует очереди или разделы JMS, их необходимо перенести на внешний размещенный сервер JMS, например, в Служебную шину Azure (см. сведения об [использовании Служебной шины как брокера сообщений](/azure/service-bus-messaging/message-transfers-locks-settlement)).

Если постоянные хранилища JMS настроены, вам нужно записать их конфигурацию и применить ее после миграции.
