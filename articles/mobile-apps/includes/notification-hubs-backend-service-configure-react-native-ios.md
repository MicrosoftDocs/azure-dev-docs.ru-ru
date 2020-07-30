---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: eefda17bfa94261984dfbd86d36bd39a32f7d0ea
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401605"
---
### <a name="configure-required-ios-packages"></a>Настройка обязательных пакетов iOS

Пакет [автоматически связывается](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) при создании приложения. Все, что нужно сделать, — это установить собственные группы pod.

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Настройка Info.plist и Entitlements.plist

1. Перейдите в папку PushDemo/ios, откройте рабочую область PushDemo.xcworkspace, выберите верхний проект PushDemo и перейдите на вкладку "Подписывание и возможности".

1. Обновите идентификатор пакета, чтобы он соответствовал значению, используемому в профиле подготовки.

1. Добавьте две новые возможности, используя кнопку со знаком плюс:

    - возможность фонового режима и удаленные уведомления о тактах;
    - возможность push-уведомлений.

### <a name="handle-push-notifications-for-ios"></a>Работа с push-уведомлениями для iOS

1. Откройте файл AppDelegate.h и добавьте следующую инструкцию import.

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Обновите список протоколов, поддерживаемых AppDelegate, добавив `UNUserNotificationCenterDelegate`.

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Откройте файл AppDelegate.m и настройте все обязательные обратные вызовы iOS.

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
