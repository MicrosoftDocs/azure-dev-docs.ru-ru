---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 20a8fa10a27a41621c2b08839682fefbd023ee2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401602"
---
Серверная часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) используется для обработки [регистрации устройств](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) для клиента с использованием [этого подхода](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations). Служба также будет отправлять push-уведомления с использованием кроссплатформенного механизма. 

Эти операции обрабатываются с помощью [пакета средств разработки Центров уведомлений для внутренних операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). См. раздел [Управление регистрацией из серверной части](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
