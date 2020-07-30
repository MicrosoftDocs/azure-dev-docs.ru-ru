---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 588a59014e01c7fc43c0c1573589be9a8776dd2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401642"
---
### <a name="create-the-xamarinforms-solution"></a>Создание решения Xamarin.Forms

1. В **Visual Studio** создайте новое решение **Xamarin.Forms**, выбрав **Приложение с пустыми формами** в качестве шаблона и указав *PushDemo* в поле **Имя проекта**.

    > [!NOTE]
    > В диалоговом окне **Настройка приложения с пустыми формами** убедитесь, что значение **Идентификатор организации** совпадает с тем значением, которое вы указали ранее, а также выбраны обе целевые платформы: **Android** и **iOS**.

1. С нажатой клавишей **CTRL** **щелкните** решение *PushDemo*, затем выберите **Обновить пакеты NuGet**.
1. С нажатой клавишей **CTRL** **щелкните** решение *PushDemo*, затем выберите **Управление пакетами NuGet**.
1. Выполните поиск по строке **Newtonsoft.json** и убедитесь, что это расширение установлено.
1. Щелкните элемент **Добавить пакеты**, а затем — элемент **Принять**, когда будет предложено принять условия лицензионного соглашения.
1. Создайте и запустите приложение на каждой целевой платформе (нажав клавиши **COMMAND** и **ВВОД**), чтобы протестировать выполнение созданного из шаблона приложения на ваших устройствах.

### <a name="implement-the-cross-platform-components"></a>Реализация кроссплатформенных компонентов

1. С нажатой клавишей **CTRL**  **щелкните** проект **PushDemo** и выберите **Создать папку** в меню **Добавить**. Затем щелкните **Добавить** и укажите *Models* в поле **Имя папки**.

1. С нажатой клавишей **CTRL** **щелкните** папку **Models**, а затем выберите **Создать файл...** в меню **Добавить**.

1. Выберите **Общие** > **Пустой класс**, введите имя *DeviceInstallation.cs* и добавьте следующую реализацию.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Добавьте **пустое перечисление** в папку **Models** с именем *PushDemoAction.cs* со следующей реализацией.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Добавьте в проект **PushDemo** новую папку с именем *Services*, добавьте в эту папку **пустой класс** с именем *ServiceContainer.cs* со следующей реализацией.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Это сокращенная версия класса [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) из репозитория [XamCAT](https://github.com/xamcat/mobcat-library). Он будет использоваться в качестве облегченного контейнера инверсии управления (IoC).

1. Добавьте **пустой интерфейс** в папку **Services** с именем *IDeviceInstallationService.cs*, а затем добавьте следующий код.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Этот интерфейс позже будет реализован и добавлен в начальную загрузку для каждой целевой платформы, чтобы поддерживать характерные для платформы возможности и сведения **DeviceInstallation**, которые нужны внутренней службе.

1. Добавьте еще один **пустой интерфейс** в папку **Services** с именем *INotificationRegistrationService.cs*, а затем добавьте следующий код.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Это обеспечит обработку взаимодействия между клиентом и внутренней службой.

1. Добавьте еще один **пустой интерфейс** в папку **Services** с именем *INotificationActionService.cs*, а затем добавьте следующий код.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Этот простой механизм используется для централизованной обработки действий уведомления.

1. Добавьте **пустой интерфейс** в папку **Services** с именем *IPushDemoNotificationActionService.cs* с наследованием от *INotificationActionService* со следующей реализацией.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Этот тип существует только в приложении **PushDemo** и использует перечисление **PushDemoAction**, чтобы определять действие, которое активируется строго типизированным способом.

1. Добавьте **пустой класс** в папку **Services** с именем *NotificationRegistrationService.cs* с реализацией *INotificationRegistrationService* со следующим кодом.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Аргумент **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).

1. Добавьте **пустой класс** в папку **Services** с именем *PushDemoNotificationActionService.cs* и реализацией *IPushDemoNotificationActionService* на основе следующего кода.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Добавьте **пустой класс** в проект **PushDemo** с именем *Config.cs* со следующей реализацией.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Это простой способ хранить секреты вне системы управления версиями. Эти значения можно заменять в ходе автоматизированной сборки или переопределять, используя локальный разделяемый класс. Это будет выполнено на следующем шаге.
    >
    > Значение для поля **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).

1. Добавьте еще один **пустой класс** в проект **PushDemo** с именем *Config.local_secrets.cs* со следующей реализацией.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Замените заполнители реальными значениями для конечной точки. Вы должны были записать их при создании внутренней службы. URL-адрес **приложения API** должен иметь вид ``https://<api_app_name>.azurewebsites.net/``. Не забудьте добавить ``*.local_secrets.*`` в файл gitignore, чтобы выполнять фиксацию этого файла.
    >
    > Значение для поля **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).

1. Добавьте **пустой класс** в проект **PushDemo** с именем *Bootstrap.cs* со следующей реализацией.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > Метод **Begin** будет вызываться на каждой платформе при запуске приложения, передавая реализацию **IDeviceInstallationService** для конкретной платформы.
    >
    > Аргумент конструктора **NotificationRegistrationService** **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).

### <a name="implement-the-cross-platform-ui"></a>Реализация кросс-платформенного пользовательского интерфейса

1. В проекте **PushDemo** откройте файл **MainPage.xaml** и замените элемент управления **StackLayout** следующим кодом.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Теперь добавьте в файл **MainPage.xaml.cs** резервное поле с атрибутом **readonly**, которое будет хранить ссылку на реализацию **INotificationRegistrationService**.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. В конструкторе **MainPage** разрешите реализацию **INotificationRegistrationService** с помощью **ServiceContainer** и присвойте ее резервной переменной *_notificationRegistrationService_*.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Реализуйте обработчики для событий **Clicked** кнопок **RegisterButton** и **DeregisterButton**, вызывая из них соответствующие методы **Register**/**Deregister**.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Теперь в **App.xaml.cs** обеспечьте наличие ссылок на следующие пространства имен.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Реализуйте обработчик событий для события **IPushDemoNotificationActionService** **ActionTriggered**.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. В конструкторе **приложения** разрешите реализацию **IPushNotificationActionService** с помощью **ServiceContainer** и создайте подписку на событие **IPushDemoNotificationActionService** **ActionTriggered**.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Здесь мы просто демонстрируем получение и распространение действий push-уведомлений. Обычно вместо предупреждения, которое мы здесь вызываем через корневой объект **Page** (**MainPage**), выполняется автоматическая обработка, например переход к конкретному представлению или обновление некоторых данных.
