---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 87509c0201407eb603f776e8b4994fc6f984cb6c
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974313"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Настройка Info.plist и Entitlements.plist

1. Убедитесь, что вы вошли в **учетную запись разработчика Apple**, открыв раздел **Visual Studio** > **Параметры...**  > **Публикация** > **Учетные записи разработчиков Apple**, а также проверьте наличие *сертификата* и *профиля подготовки*. Эти ресурсы должны были быть созданы на предыдущих этапах.

1. В проекте **PushDemo.iOS** откройте файл **Info.plist** и убедитесь, что значение **BundleIdentifier** соответствует значению, которое использовалось для соответствующего профиля подготовки на [портале разработчика Apple](https://developer.apple.com). Значение **BundleIdentifier** имеет формат ``com.<organization>.PushDemo``.

1. В том же файле для параметра **Minimum system version** (Минимальная версия системы) задайте значение **13.0**.

    > [!NOTE]
    > Для задач, выполняемых в этом руководстве, поддерживаются только устройства с **iOS 13.0 и более поздних версий**, но вы можете расширить поддержку до устройств с более ранними версиями.

1. Откройте раздел **Параметры проекта** для проекта **PushDemo.iOS** (например, двойным щелчком имени проекта).

1. В разделе **Параметры проекта** откройте раздел **Сборка > Подписывание пакета iOS** и убедитесь, что в поле **Команда** выбрана правильная учетная запись разработчика. Затем убедитесь, что установлен флажок "Automatically manage signing" (Автоматическое управление подписями) и автоматически выбирается правильное значение "Сертификат для подписи и подготовительный профиль".

    > [!NOTE]
    > Если значения *Сертификат для подписи* и *Профиль обеспечения* не выбираются автоматически, выберите вариант **Подготовка вручную** и щелкните **Bundle Signing Options** (Варианты подписывания пакета). Убедитесь, что правильно выбрана *команда* для параметра **Удостоверение подписывания**, а профиль обеспечения *PushDemo* выбран в поле **Профиль обеспечения** для обеих конфигураций (**Отладка** и **Выпуск**), а также в обеих указано значение **iPhone** для параметра **Платформа**.

1. В проекте **PushDemo.iOS** откройте файл **Entitlements.plist** и убедитесь, что установлен флажок **Включить push-уведомления** на вкладке **Права**. Теперь проверьте, задано ли для параметра **Среда APS** значение **Разработка** на вкладке **Исходный код**.

### <a name="handle-push-notifications-for-ios"></a>Работа с push-уведомлениями для iOS

1. С нажатой клавишей **CTRL** + **щелкните** проект **PushDemo.iOS**. Затем выберите элемент **Создать папку** в меню **Добавить**, щелкните **Добавить** и укажите *Services* в качестве значения для параметра **Имя папки**.

1. С нажатой клавишей **CTRL** + **щелкните** папку **Services**. Затем выберите элемент **Новый файл...** в меню **Добавить**.

1. Выберите элементы **Общие** > **Пустой класс**. Введите *DeviceInstallationService.cs* в поле **Имя** и щелкните элемент **Новый**, чтобы добавить указанную ниже реализацию.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.IsNullOrWhiteSpace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс предоставляет уникальный идентификатор (с использованием значения [UIDevice.IdentifierForVendor](/dotnet/api/uikit.uidevice.identifierforvendor)) и полезные данные для регистрации в центре уведомлений.

1. Добавьте в проект **PushDemo.iOS** новую папку с именем *Extensions*. Затем добавьте в эту папку **пустой класс** с именем *NSDataExtensions.cs* и вставьте в него указанную ниже реализацию.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. В файле **AppDelegate.cs** обязательно добавьте в самое начало приведенные далее пространства имен.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Добавьте закрытые свойства и соответствующие им поля для хранения ссылок на реализации **IPushDemoNotificationActionService**, **INotificationRegistrationService** и  **IDeviceInstallationService**.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    INotificationRegistrationService _notificationRegistrationService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    INotificationRegistrationService NotificationRegistrationService
        => _notificationRegistrationService ??
            (_notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Добавьте метод **RegisterForRemoteNotifications**, который будет регистрировать параметры уведомлений пользователю, а затем удаленные уведомления с помощью **APNs**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Добавьте метод **CompleteRegistrationAsync** для присвоения значения свойству `IDeviceInstallationService.Token`. Обновите регистрацию и кэшируйте маркер устройства, если он изменился с момента последнего сохранения.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Добавьте метод **ProcessNotificationActions** для обработки данных оповещения **NSDictionary** и условного вызова **NotificationActionService.TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Переопределите метод **RegisteredForRemoteNotifications**, чтобы передавать аргумент **deviceToken** в метод **CompleteRegistrationAsync**.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Переопределите метод **ReceivedRemoteNotification**, чтобы передавать аргумент **userInfo** в метод **ProcessNotificationActions**.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Переопределите метод **FailedToRegisterForRemoteNotifications**, чтобы сохранить сведения об ошибке в журнал.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Это, по сути, просто заполнитель. Для рабочих сценариев вам лучше создать нормальную реализацию ведения журнала и обработки ошибок.

1. Обновите метод **FinishedLaunching**, чтобы вызвать `Bootstrap.Begin` сразу после вызова `Forms.Init` и передавать ему реализацию **IDeviceInstallationService** для конкретной платформы.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. В том же методе организуйте условный запрос проверки подлинности и регистрацию удаленных уведомлений сразу после `Bootstrap.Begin`.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. В том же методе **FinishedLaunching** добавьте вызов **ProcessNotificationActions** сразу после вызова `LoadApplication`, если аргумент **options** содержит ключ **UIApplication.LaunchOptionsRemoteNotificationKey** с объектом результата **userInfo**.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
