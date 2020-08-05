---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9336229d7dfcf65277b9bd0f9a0a01e444609d5e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401652"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Настройка целевого средства выполнения тестов и файла Info.plist

1. В **Visual Studio Code**, удерживая нажатой клавишу **CTRL**, **щелкните** папку **ios**, а затем выберите **Открыть в XCode**.

1. В **Xcode**щелкните **Runner** (Средство выполнения тестов) (**xcodeproj** вверху, а не папка), затем выберите целевой объект **Runner** (Средство выполнения тестов), а затем **Signing & Capabilities** (Подписывание и возможности). Выбрав конфигурацию сборки **All** (Все), выберите учетную запись разработчика для **Team** (Команда). Убедитесь, что флажок Automatically manage signing (Автоматическое управление подписями) установлен и автоматически выбраны правильные значения Signing Certificate (Сертификат для подписи) и Provisioning Profile (Профиль подготовки).

    > [!NOTE]
    > Если новое значение профиля подготовки не отображается, попробуйте обновить профили для удостоверения подписывания, выбрав **Xcode** > **Preferences** > **Account** (Xcode > Настройки > Учетная запись), а затем нажмите кнопку **Download Manual Profiles** (Скачать профили вручную), чтобы скачать профили.

1. Щелкните **+ Capability** (+ Возможности), а затем выполните поиск по запросу **Push Notifications** (Push-уведомления). **Дважды щелкните** **Push Notifications** (Push-уведомления), чтобы добавить эту возможность.

1. Откройте файл **Info.plist** и для параметра **Minimum system version** (Минимальная версия системы) задайте значение **13.0**.

    > [!NOTE]
    > Для задач, выполняемых в этом руководстве, поддерживаются только устройства с **iOS 13.0 и более поздних версий**, но вы можете расширить поддержку до устройств с более ранними версиями.

1. Откройте файл **Runner.entitlements** и проверьте, задано ли для параметра **APS Environment** (Среда APS) значение **development** (Разработка).

### <a name="handle-push-notifications-for-ios"></a>Работа с push-уведомлениями для iOS

1. Удерживая нажатой клавишу **CTRL**, **щелкните** папку **Runner** (в проекте Runner), выберите **New Group** (Создать группу) и укажите **Services** в качестве имени.

1. Удерживая нажатой клавишу **CTRL**, **щелкните** папку **Services**, а затем выберите **New File…** (Создать файл…). Затем выберите **Swift File** (Файл Swift) и нажмите кнопку **Next** (Далее). Укажите в качестве имени **DeviceInstallationService**, а затем щелкните **Create** (Создать).

1. Реализуйте **DeviceInstallationService.swift** с помощью следующего кода.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/deviceinstallation` для конкретной платформы. Это было определено в части Flutter приложения в файле **DeviceInstallationService.dart**. В этом случае вызовы выполняются из общего кода на собственном узле. Не забудьте заменить **<ваша_организация>** именем своей организации во всех вхождениях.
    >
    > Этот класс предоставляет уникальный идентификатор (с использованием значения [UIDevice.IdentifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor)) в составе полезных данных для регистрации в центре уведомлений.

1. Добавьте в папку **Services** еще один **файл Swift** с именем *NotificationRegistrationService*, а затем добавьте следующий код.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/notificationregistration` для конкретной платформы. Это было определено в части Flutter приложения в файле **NotificationRegistrationService.dart**. В этом случае вызовы выполняются из собственного узла в общем коде. Не забудьте заменить **<ваша_организация>** именем своей организации во всех вхождениях.

1. Добавьте в папку **Services** еще один **файл Swift** с именем *NotificationActionService*, а затем добавьте следующий код.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/notificationaction` для конкретной платформы. Это было определено в части Flutter приложения в файле **NotificationActionService.dart**. В этом случае вызовы могут выполняться в обоих направлениях. Не забудьте заменить **<ваша_организация>** именем своей организации во всех вхождениях.

1. В файле **AppDelegate.swift** добавьте переменные для хранения ссылки на ранее созданные службы.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Добавьте функцию с именем **processNotificationActions** для обработки данных уведомлений. Условно активируйте это действие или сохраните его для дальнейшего использования, если это действие обрабатывается во время запуска приложения.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Переопределите функцию **didRegisterForRemoteNotificationsWithDeviceToken**, задав для **DeviceInstallationService** значение **token**. Затем вызовите **refreshRegistration** в **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Переопределите функцию **didReceiveRemoteNotification**, передав аргумент **userInfo** в функцию **processNotificationActions**.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Переопределите функцию **didFailToRegisterForRemoteNotificationsWithError**, чтобы зарегистрировать ошибку в журнале.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Это, по сути, просто заполнитель. Для рабочих сценариев вам лучше создать нормальную реализацию ведения журнала и обработки ошибок.

1. В **didFinishLaunchingWithOptions** создайте экземпляры переменных **deviceInstallationService**, **notificationRegistrationService** и **notificationActionService**.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. В той же функции создайте условный запрос проверки подлинности и регистрацию удаленных уведомлений.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Если **launchOptions** содержит ключ **remoteNotification**, вызовите **processNotificationActions** в конце функции **didFinishLaunchingWithOptions**. Передайте полученный объект **userInfo** и используйте *true* в качестве значения аргумента *launchAction*. Значение *true* означает, что действие обрабатывается при запуске приложения.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
