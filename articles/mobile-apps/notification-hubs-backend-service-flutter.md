---
title: Отправка push-уведомлений в приложения Flutter с помощью Центров уведомлений Azure и внутренней службы | Документация Майкрософт
description: Узнайте, как отправлять push-уведомления в приложения Flutter, которые используют Центры уведомлений Azure через внутреннюю службу.
author: mikeparker104
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: miparker
ms.openlocfilehash: b9c5f45dfa95af43ec3b56ca9c86957fcd1b7b88
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810657"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Руководство по Отправка push-уведомлений в приложения Flutter с помощью Центров уведомлений Azure и внутренней службы  

[![Скачать пример](media/download.png) Скачайте пример](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

В этом руководстве показано, как использовать [Центры уведомлений Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) для отправки push-уведомлений в приложение [Flutter](https://flutter.dev), предназначенное для **Android** и **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

В этом руководстве описываются следующие шаги:

> [!div class="checklist"]
>
> * [настройка служб push-уведомлений и Центров уведомлений Azure;](#set-up-push-notification-services-and-azure-notification-hub)
> * [создание внутреннего приложения веб-API на ASP.NET Core;](#create-an-aspnet-core-web-api-backend-application)
> * [создание кросс-платформенного приложения React Native;](#create-a-cross-platform-flutter-application)
> * [настройка push-уведомлений в собственном проекте Android;](#configure-the-native-android-project-for-push-notifications)
> * [настройка push-уведомлений в собственном проекте iOS;](#configure-the-native-ios-project-for-push-notifications)
> * [тестирование решения.](#test-the-solution)

## <a name="prerequisites"></a>Предварительные требования

Для работы вам потребуется следующее:

* [подписка Azure](https://portal.azure.com), где можно создавать ресурсы и управлять ими;
* набор средств [Flutter](https://flutter.dev/docs/get-started/install) (вместе с требуемыми компонентами);
* [Visual Studio Code](https://code.visualstudio.com) с установленными [подключаемыми модулями Flutter и Dart](https://flutter.dev/docs/get-started/editor?tab=vscode);
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) для управления зависимостями библиотек.
* возможность запускать приложение на **Android** (физические или эмулированные устройства) или **iOS** (только физические устройства).

Для Android необходимо иметь следующее:

* разблокированное для разработки физическое устройство или эмулятор  *(с API версии 26 или более поздней и установленными Сервисами Google Play)* .

Для iOS требуется следующее:

* активная [учетная запись разработчика Apple](https://developer.apple.com);
* физическое устройство iOS, которое [зарегистрировано в учетной записи разработчика](https://help.apple.com/developer-account/#/dev40df0d9fa) *(под управлением iOS 13.0 или более поздней версии)* ;
* [сертификат разработки](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12**, установленный в **цепочке ключей**, который позволяет [запускать приложение на физическом устройстве](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> Симулятор iOS не поддерживает удаленные уведомления, поэтому для изучения этого примера в среде iOS требуется физическое устройство. Но для целей этого руководства вам не обязательно запускать приложение и на **Android**, и на **iOS**.

Вам не потребуется наличие опыта для выполнения действий, которые описаны в этом примере демонстрации общих концепций. Но знакомство со следующими аспектами может оказаться полезным.

* [Портал разработчика Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Консоль Google Firebase](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) и [отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure](https://docs.microsoft.com/azure/notification-hubs/ios-sdk-get-started)
* [Flutter](https://flutter.dev) и [Dart](https://dart.dev) для кросс-платформенной разработки
* [Kotlin](https://kotlinlang.org) и [Swift](https://developer.apple.com/swift) для разработки нативных приложений для Android и iOS

Приведенные инструкции относятся к [macOS](https://developer.apple.com/macos). Их можно выполнить и в [Windows](https://www.microsoft.com/windows), пропуская аспекты, касающиеся **iOS**.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Настройка служб push-уведомлений и Центров уведомлений Azure

В этом разделе объясняется, как настроить **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** и **[Службу push-уведомлений Apple (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Затем вы создадите и настроите центр уведомлений для работы с этими службами.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Создание внутреннего приложения веб-API на ASP.NET Core

В этом разделе показано, как создать серверную часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) для [регистрации устройств](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) и отправки уведомлений в мобильное приложение Flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Создание кросс-платформенного приложения Flutter

В этом разделе показано, как создать мобильное приложение [Flutter](https://flutter.dev), которое реализует кросс-платформенный механизм push-уведомлений.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте Android

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте iOS

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Тестирование решения

Теперь вы готовы протестировать отправку уведомлений через внутреннюю службу.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>Устранение неполадок

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Связанные ссылки

* [Общие сведения о Центрах уведомлений Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)
* [Установка Flutter в macOS](https://flutter.dev/docs/get-started/install/macos)
* [Установка Flutter в Windows](https://flutter.dev/docs/get-started/install/windows)
* [Пакет средств разработки Центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Пакет средств разработки для Центров уведомлений на GitHub](https://github.com/Azure/azure-notificationhubs)
* [Регистрация в серверной части приложения](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification)
* [Управление регистрацией](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management)
* [Работа с тегами](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-tags-segment-push-message)
* [Работа с пользовательскими шаблонами](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть простое приложение Flutter, подключенное к концентратору уведомлений через внутреннюю службу, которое может отправлять и получать уведомления.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]
