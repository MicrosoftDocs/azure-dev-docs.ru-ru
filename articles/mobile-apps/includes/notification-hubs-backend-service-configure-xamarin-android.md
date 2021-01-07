---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: faacb54f9e8a16cf4ef5b39aed0a3e42a4b3f255
ms.sourcegitcommit: 84f64dec74b4b041b8830a4e7489e22f0e943440
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97866132"
---
### <a name="validate-package-name-and-permissions"></a>Проверка имени пакета и разрешений

1. В проекте **PushDemo.Android** откройте раздел **Параметры проекта**, а затем выберите элемент **Приложение Android** из раздела **Сборка**.

1. Убедитесь, что значение **Имя пакета** совпадает с тем, которое вы указали в [консоли Firebase](https://console.firebase.google.com) для проекта **PushDemo**. **Имя пакета** имеет формат ``com.<organization>.pushdemo``.

1. Для параметра **Минимальная версия Android** установите значение **Android 8.0 (API уровня 26)** . **Целевая версия Android** должна соответствовать последней версии **уровня API**.

    > [!NOTE]
    > Для задач, выполняемых в этом руководстве, поддерживаются только устройства с **API уровня 26 и выше**, но вы можете расширить поддержку до устройств с более ранними версиями API.

1. Убедитесь, что включены разрешения **INTERNET** и **READ_PHONE_STATE** в разделе **Необходимые разрешения**.

1. Нажмите кнопку **ОК**.

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Добавление пакетов Xamarin.GooglePlayServices.Base и Xamarin.Firebase.Messaging

1. В разделе **PushDemo.Android** с нажатой клавишей **CTRL** + **щелкните** папку **Packages**. Затем выберите **Управление пакетами NuGet...** .

1. Найдите пакет **Xamarin.GooglePlayServices.Base** (не **Basement**) и убедитесь, что он отмечен.

1. Найдите пакет **Xamarin.Firebase.Messaging** и убедитесь, что он отмечен.

1. Щелкните элемент **Добавить пакеты**, а затем — элемент **Принять**, когда будет предложено принять **условия лицензионного соглашения**.

### <a name="add-the-google-services-json-file"></a>Добавление JSON-файла сервисов Google

1. С нажатой клавишей **CTRL** + **щелкните** проект `PushDemo.Android`. Затем выберите элемент **Существующий файл...** в меню **Добавить**.

1. Выберите файл *google-services.json*, который вы скачали ранее при настройке проекта **PushDemo** в [консоли Firebase](https://console.firebase.google.com). Затем щелкните **Открыть**.

1. Выберите элемент **Скопировать файл в каталог**, когда появится соответствующий запрос.

1. С нажатой клавишей **CTRL** + **щелкните** файл *google-services.json* из проекта `PushDemo.Android`. Убедитесь, что **GoogleServicesJson** указано в качестве **действия сборки**.

### <a name="handle-push-notifications-for-android"></a>Работа с push-уведомлениями для Android

1. С нажатой клавишей **CTRL** + **щелкните** проект `PushDemo.Android`. Затем выберите элемент **Создать папку** в меню **Добавить**, щелкните команду **Добавить** и укажите *Services* в качестве значения для параметра **Имя папки**.

1. С нажатой клавишей **CTRL** + **щелкните** папку **Services**. Затем выберите элемент **Новый файл...** в меню **Добавить**.

1. Выберите элементы **Общие** > **Пустой класс**. Введите *DeviceInstallationService.cs* в поле **Имя** и щелкните элемент **Новый**, чтобы добавить указанную ниже реализацию.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.IsNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс предоставляет уникальный идентификатор (с использованием [Secure.AndroidId](/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) в составе полезных данных при регистрации в центре уведомлений.

1. Добавьте в папку **Services** еще один **пустой класс** с именем *PushNotificationFirebaseMessagingService.cs* и поместите в него указанную ниже реализацию.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
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

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. В файле **MainActivity.cs** обязательно добавьте в самое начало приведенные далее ниже пространства имен.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. В файле **MainActivity.cs** задайте для параметра **LaunchMode** значение **SingleTop**, чтобы избежать повторного создания **MainActivity** при открытии.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Добавьте частные свойства и соответствующие им поля для хранения ссылок на реализации **IPushNotificationActionService** и **IDeviceInstallationService**.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Реализуйте интерфейс **IOnSuccessListener**, который будет получать и сохранять маркер **Firebase**.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Добавьте новый метод с именем **ProcessNotificationActions**. Он проверит наличие дополнительного значения с именем *action* в полученном **Intent**. Вызывайте это действие с условием через реализацию **IPushDemoNotificationActionService**.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Переопределите метод **OnNewIntent**, чтобы он вызывал метод **ProcessNotificationActions**.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Так как параметр **LaunchMode** для **Activity** имеет значение **SingleTop**, объект **Intent** будет отправляться существующему экземпляру **Activity** с помощью метода **OnNewIntent**, а не метода **OnCreate**. Это означает, что вам необходимо обрабатывать входящее намерение в обоих методах: **OnCreate** и **OnNewIntent**.

1. Обновите метод **OnCreate**, чтобы вызвать `Bootstrap.Begin` сразу после вызова `base.OnCreate` и передавать ему реализацию **IDeviceInstallationService** для конкретной платформы.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. В том же методе добавьте вызов с условием для **GetInstanceId** из экземпляра **FirebaseApp** сразу после вызова `Bootstrap.Begin`, указав **MainActivity** в качестве значения для **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. В том же **OnCreate** вызывайте **ProcessNotificationActions** немедленно после вызова `LoadApplication` с передачей ему текущего значения **Intent**.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Приложение необходимо регистрировать повторно при каждом запуске и остановке из сеанса отладки, чтобы и по-прежнему получать push-уведомления.
