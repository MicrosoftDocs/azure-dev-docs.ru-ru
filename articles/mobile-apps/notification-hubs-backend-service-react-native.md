---
title: Отправка push-уведомлений в приложения React Native с помощью Центров уведомлений Azure и внутренней службы | Документация Майкрософт
description: Узнайте, как отправлять push-уведомления в приложения React Native, которые используют Центры уведомлений Azure через внутреннюю службу.
author: alexeystrakh
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: alstrakh
ms.openlocfilehash: ac6b1cc183cd06f8186a678e006602c57a54238e
ms.sourcegitcommit: 54f976887d218aaabd94371e24809716da8cf86e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554341"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Руководство по отправке push-уведомлений в приложения React Native с помощью Центров уведомлений Azure и внутренней службы  

[![Скачать пример](media/download.png) Скачайте пример](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

В этом руководстве показано, как использовать [Центры уведомлений Azure](/azure/notification-hubs/notification-hubs-push-notification-overview) для отправки push-уведомлений в приложение [React Native](https://reactnative.dev/), предназначенное для **Android** и **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

В этом руководстве описываются следующие шаги:

> [!div class="checklist"]
>
> * [настройка служб push-уведомлений и Центров уведомлений Azure;](#set-up-push-notification-services-and-azure-notification-hub)
> * [создание внутреннего приложения веб-API на ASP.NET Core;](#create-an-aspnet-core-web-api-backend-application)
> * [создание кросс-платформенного приложения React Native;](#create-a-cross-platform-react-native-application)
> * [настройка push-уведомлений в собственном проекте Android;](#configure-the-native-android-project-for-push-notifications)
> * [настройка push-уведомлений в собственном проекте iOS;](#configure-the-native-ios-project-for-push-notifications)
> * [тестирование решения.](#test-the-solution)

## <a name="prerequisites"></a>Предварительные требования

Для работы вам потребуется следующее:

* [подписка Azure](https://portal.azure.com), где можно создавать ресурсы и управлять ими;
* компьютер Mac с установленной средой [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) (или ПК, на котором работает [Visual Studio 2019](https://visualstudio.microsoft.com/vs) с рабочей нагрузкой **Разработка мобильных приложений на .NET**).
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

* [Портал разработчиков Azure](https://developer.apple.com)
* [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core)
* [Консоль Google Firebase](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) и [отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure](/azure/notification-hubs/ios-sdk-get-started)
* [React Native](https://reactnative.dev/docs/getting-started).

Здесь описаны действия, выполняемые в [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и [Visual Studio Code](https://code.visualstudio.com/download), но также поддерживается и [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Настройка служб push-уведомлений и Центров уведомлений Azure

В этом разделе объясняется, как настроить **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** и **[Службу push-уведомлений Apple (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Затем вы создадите и настроите центр уведомлений для работы с этими службами.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Создание внутреннего приложения веб-API на ASP.NET Core

В этом разделе показано, как создать серверную часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) для обработки [регистрации устройств](/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) и отправки уведомлений в мобильное приложение React Native.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Создание кросс-платформенного приложения React Native

В этом разделе показано, как создать мобильное приложение [React Native](https://reactnative.dev/), которое реализует кроссплатформенный механизм push-уведомлений.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](includes/notification-hubs-backend-service-sample-app-react-native.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте Android

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-react-native-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте iOS

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-react-native-ios.md)]

## <a name="test-the-solution"></a>Тестирование решения

Теперь вы готовы протестировать отправку уведомлений через внутреннюю службу.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>Устранение неполадок

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Связанные ссылки

* [Общие сведения о Центрах уведомлений Azure](/azure/notification-hubs/notification-hubs-push-notification-overview)
* [Установка Visual Studio для Mac](/visualstudio/mac/installation)
* [Установка Visual Studio Code](https://code.visualstudio.com/download)
* [Настройка среды разработки React Native](https://reactnative.dev/docs/environment-setup)
* [Пакет средств разработки Центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Пакет средств разработки для Центров уведомлений на GitHub](https://github.com/Azure/azure-notificationhubs)
* [Регистрация в серверной части приложения](/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification)
* [Управление регистрацией](/azure/notification-hubs/notification-hubs-push-notification-registration-management)
* [Работа с тегами](/azure/notification-hubs/notification-hubs-tags-segment-push-message)
* [Работа с пользовательскими шаблонами](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть простое приложение React Native, подключенное к концентратору уведомлений через внутреннюю службу, которое может отправлять и получать уведомления.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]