---
title: Отправка push-уведомлений в приложения Xamarin.Forms с помощью Центров уведомлений Azure и внутренней службы | Документация Майкрософт
description: Сведения о том, как отправлять push-уведомления в приложения Xamarin.Forms, которые используют Центры уведомлений Azure через внутреннюю службу.
author: mikeparker104
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: miparker
ms.openlocfilehash: 308e727f57ed086899d4fb5906235cb5a17bda16
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337190"
---
# <a name="tutorial-send-push-notifications-to-xamarinforms-apps-using-azure-notification-hubs-via-a-backend-service"></a>Руководство по отправке push-уведомлений в приложения Xamarin.Forms с помощью Центров уведомлений Azure и внутренней службы  

[![Скачать пример](media/download.png) Скачайте пример](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

В этом руководстве вы примените [Центры уведомлений Azure](/azure/notification-hubs/notification-hubs-push-notification-overview) для отправки push-уведомлений в приложение [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms), нацеленное на платформы **Android** и **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

В этом руководстве описываются следующие шаги:

> [!div class="checklist"]
>
> * [настройка служб push-уведомлений и Центров уведомлений Azure;](#set-up-push-notification-services-and-azure-notification-hub)
> * [создание внутреннего приложения веб-API на ASP.NET Core;](#create-an-aspnet-core-web-api-backend-application)
> * [создание кросс-платформенного приложения Xamarin.Forms;](#create-a-cross-platform-xamarinforms-application)
> * [настройка push-уведомлений в собственном проекте Android;](#configure-the-native-android-project-for-push-notifications)
> * [настройка push-уведомлений в собственном проекте iOS;](#configure-the-native-ios-project-for-push-notifications)
> * [тестирование решения.](#test-the-solution)

## <a name="prerequisites"></a>Предварительные требования

Для работы вам потребуется следующее:

* [подписка Azure](https://azure.microsoft.com/free/dotnet), где можно создавать ресурсы и управлять ими;
* компьютер Mac с установленной средой [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) или ПК, на котором работает [Visual Studio 2019](https://visualstudio.microsoft.com/vs).
* Пользователи [Visual Studio 2019](https://visualstudio.microsoft.com/vs) также должны установить такие рабочие нагрузки: **Разработка мобильных приложений на .NET** и **ASP.NET и разработка веб-приложений**.
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
* [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1) и [веб-API](https://dotnet.microsoft.com/apps/aspnet/apis)
* [Консоль Google Firebase](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) и [отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure](/azure/notification-hubs/ios-sdk-get-started)
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin) и [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms)

> [!IMPORTANT]
> Следующие инструкции относятся к [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/). Можно выполнить эти инструкции и в [Visual Studio 2019](https://visualstudio.microsoft.com/vs), но, возможно, потребуется согласовать некоторые различия, например описания пользовательского интерфейса и рабочих процессов, имена шаблонов, конфигурации среды и т. д.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Настройка служб push-уведомлений и Центров уведомлений Azure

В этом разделе объясняется, как настроить **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** и **[Службу push-уведомлений Apple (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Затем вы создадите и настроите центр уведомлений для работы с этими службами.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Создание внутреннего приложения веб-API на ASP.NET Core

В этом разделе объясняется, как создать внутреннюю часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) для управления [регистрацией устройств](/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) и отправки уведомлений в мобильное приложение Xamarin.Forms.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-xamarinforms-application"></a>Создание кросс-платформенного приложения Xamarin.Forms

В этом разделе объясняется, как создать мобильное приложение [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms), которое реализует кроссплатформенный механизм push-уведомлений.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Xamarin.Forms application](includes/notification-hubs-backend-service-sample-app-xamarin-forms.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте Android

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-xamarin-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Настройка push-уведомлений в собственном проекте iOS

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-xamarin-ios.md)]

## <a name="test-the-solution"></a>Тестирование решения

Теперь вы готовы протестировать отправку уведомлений через внутреннюю службу.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>Устранение неполадок

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Связанные ссылки

* [Общие сведения о Центрах уведомлений Azure](/azure/notification-hubs/notification-hubs-push-notification-overview)
* [Установка Visual Studio для Mac](/visualstudio/mac/installation?view=vsmac-2019)
* [Установка Xamarin в Windows](/xamarin/get-started/installation/windows)
* [Пакет средств разработки Центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Пакет средств разработки для Центров уведомлений на GitHub](https://github.com/Azure/azure-notificationhubs)
* [Регистрация в серверной части приложения](/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification)
* [Управление регистрацией](/azure/notification-hubs/notification-hubs-push-notification-registration-management)
* [Работа с тегами](/azure/notification-hubs/notification-hubs-tags-segment-push-message)
* [Работа с пользовательскими шаблонами](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть простое приложение Xamarin.Forms, подключенное к концентратору уведомлений через внутреннюю службу, которое может отправлять и получать уведомления.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]
