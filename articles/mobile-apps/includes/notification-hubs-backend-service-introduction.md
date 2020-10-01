---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 98e39efca4ddaacb0e46bf5e42bd2df496529beb
ms.sourcegitcommit: e97cb81a245ce7dcabeac3260abc3db7c30edd79
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91493169"
---
Серверная часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) используется для обработки [регистрации устройств](/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) для клиента с использованием [этого подхода](/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations). Служба также будет отправлять push-уведомления с использованием кроссплатформенного механизма. 

Эти операции обрабатываются с помощью [пакета средств разработки Центров уведомлений для внутренних операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). См. раздел [Управление регистрацией из серверной части](/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).