---
title: Разные этапы разработки мобильных приложений при использовании Центра приложений Visual Studio и служб Azure
description: Сведения о разных этапах разработки мобильных приложений и о том, как с помощью Центра приложений Visual Studio и других служб Майкрософт создать высококлассное мобильное приложение.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-1234-5678-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: d1e20d59f238f6f0abb9438550ae541cfe7598a7
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632676"
---
# <a name="different-stages-in-mobile-application-development"></a>Разные этапы разработки мобильных приложений

Создание мобильного приложения состоит из нескольких этапов. Вы можете легко применить разные службы, средства и технологии Майкрософт для создания высококлассных мобильных приложений.

## <a name="app-development"></a>Разработка приложений

Вы можете создавать клиентские приложения для одной конкретной платформы на таких языках, как Objective-C и Java. Можете создавать кросс-платформенные приложения на основе Xamarin, React Native и Unity. Вы также можете создавать гибридные приложения с помощью Cordova. Для создания клиентских приложений используйте среды IDE и редакторы кода, например Visual Studio, Visual Studio для Mac или Visual Studio Code на ПК или компьютерах Mac.

## <a name="plan-and-design-with-back-end-services"></a>Планирование и проектирование с помощью серверных служб

Корпорация Майкрософт предоставляет разнообразные службы для настройки защищенной, масштабируемой и надежной серверной части для мобильного приложения. Вы можете создавать бессерверные интерфейсы API и триггеры, не отвлекаясь на обслуживание собственных серверов. Примените push-уведомления, синхронизацию автономных данных, проверку подлинности и службы данных для создания комплексной и широкой серверной части мобильного приложения с поддержкой разных сценариев разработки приложений.

При создании внутренней службы нужно учесть множество требований. Для этого необходимо:

- Ограничьте хранение данных на устройстве.
- Синхронизируйте данные между несколькими устройствами.
- Обеспечьте возможность работы приложения без подключения к сети.
- Отправляйте пользователям уведомления.
- Ограничьте потребление заряда аккумулятора.
- Создайте масштабируемую, надежную и защищенную внутреннюю часть.
- Автоматизируйте обслуживание сервера.

## <a name="devops-and-continuous-monitoring"></a>DevOps и непрерывный мониторинг

Для создания мобильных и облачных приложений можно применить возможности непрерывной интеграции (CI), непрерывной поставки (CD) и непрерывного мониторинга, что позволит создавать нативные сборки для разных платформ. Тесты автоматизации пользовательского интерфейса можно выполнять на тысячах физических устройств, автоматизировать их выпуски через бета-каналы тестирования или напрямую развертывать их в магазинах приложений. Вы также можете собирать для приложений аналитику использования, информацию об ошибках и исключениях.

## <a name="additional-services"></a>Дополнительные службы

В зависимости от типа и природы создаваемого приложения в нем можно применить многие другие службы Azure.

- [Когнитивный поиск Azure](azure-search.md) поддерживает быстрый поиск по внутренним данным любого типа.
- [Azure Cognitive Services](cognitive-services.md) предоставят приложению возможности искусственного интеллекта.
- Azure SignalR обеспечит для приложения [взаимодействие в реальном времени](real-time-communication.md).