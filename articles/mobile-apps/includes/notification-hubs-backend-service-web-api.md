---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9d7db7db5a1b7323bd10e7e9cc87ca3b9a95826a
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401675"
---
### <a name="create-a-web-project"></a>Создание веб-проекта

1. В **Visual Studio** выберите **Файл** > **Создать решение**.

1. Выберите **.NET Core** > **Приложение** > **ASP.NET Core** > **API** > **Далее**.
  
1. В диалоговом окне **Configure your new ASP.NET Core Web API** (Настройка нового веб-API ASP.NET Core) выберите **Целевая рабочая среда** в **.NET Core 3.1**.

1. Введите *PushDemoApi* в поле **Имя проекта**, а затем выберите **Создать**.

1. Чтобы протестировать приложение-шаблон, запустите отладку (**Команда** + **Ввод**).

    > [!NOTE]
    > Приложение-шаблон настроено для использования **WeatherForecastController** в качестве*launchUrl*. Это значение задано в файле **Properties** > **launchSettings.json**.  
    >
    > Если вы увидите сообщение **Обнаружен недопустимый сертификат разработки**:
    >
    > 1. Нажмите кнопку **Да**, чтобы согласиться на запуск средства "dotnet dev-certs https" и устранить эту проблему. После этого средство dotnet dev-certs https выдаст запрос на ввод пароля для сертификата и пароля для цепочки ключей.
    >
    > 1. Нажмите кнопку **Да** при появлении запроса **Install and trust the new certificate** (Установить новый сертификат и доверять ему), а затем введите пароль для цепочки ключей.

1. Разверните папку **Controllers**, а затем удалите класс **WeatherForecastController.cs**.

1. Удалите класс **WeatherForecast.cs**.

1. **CTRL** + **щелчок** по проекту **PushDemoApi**, а затем выберите **Создать файл...** в меню **Добавить**.

1. Настройте значения локальной конфигурации с помощью средства [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager) (Диспетчер секретов). Отделение секретов от решения гарантирует, что они не будут находиться в системе управления версиями. Откройте **терминал**, перейдите в каталог файла проекта и выполните следующие команды:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Замените значения заполнителей именем своего центра уведомлений и значениями строки подключения. Вы записали их в разделе [Создание концентратора уведомлений](#create-a-notification-hub). Если вы не сделали этого, их можно просмотреть в [Azure](https://portal.azure.com).

    **NotificationsHub:Name**:  
    См. *Имя* в сводке **Главное** в верхней части раздела **Обзор**.  

    **NotificationHub:ConnectionString**:  
    См. раздел *DefaultFullSharedAccessSignature* в разделе **Политики доступа**.

    > [!NOTE]
    > В рабочих сценариях можно выбрать такие варианты, как [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault) для безопасного хранения строки подключения. Для простоты секреты будут добавлены в параметры приложения [Служба приложений Azure](https://azure.microsoft.com/services/app-service/).

### <a name="authenticate-clients-using-an-api-key-optional"></a>Проверка подлинности клиентов с помощью ключа API (необязательно)

Ключи API не так безопасны, как токены, но этого вполне достаточно для целей данного учебника. Ключ API можно легко настроить с помощью [ПО промежуточного слоя ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Добавьте **ключ API** к значениям локальной конфигурации.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Замените заполнитель собственным значением и запишите его.

1. **CTRL** + **щелчок** по проекту **PushDemoApi**, затем выберите **Создать папку** из меню **Добавить**, затем щелкните **Добавить** и укажите *Authentication* в качестве значения для параметра **Имя папки**.

1. С нажатой клавишей **CTRL** **щелкните** папку **Authentication**, затем выберите **Создать файл...** в меню **Добавить**.

1. Выберите **Общие** > **Пустой класс**, введите *ApiKeyAuthOptions.cs* в поле **Имя**, а затем щелкните **Создать**, чтобы добавить следующую реализацию.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Добавьте еще один **Пустой класс** в папке **Authentication** с именем *ApiKeyAuthHandler.cs*, а затем добавьте следующую реализацию.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [Authentication Handler](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) — это тип, который реализует поведение схемы, в данном случае настраиваемую схему ключей API.

1. Добавьте еще один **Пустой класс** в папку **Authentication** с именем *ApiKeyAuthenticationBuilderExtensions.cs*, а затем добавьте следующую реализацию.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Этот метод расширения упрощает код конфигурации ПО промежуточного слоя в **Startup.cs**, делает его более удобочитаемым и в целом проще в выполнении.

1. В **Startup.cs** обновите метод **ConfigureServices**, чтобы настроить проверку подлинности ключа API ниже вызова метода **services.AddControllers**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. По-прежнему в **Startup.cs** обновите метод **Configure**, чтобы вызвать методы **UseAuthentication** и **UseAuthorization** в **IApplicationBuilder**. Убедитесь, что эти методы вызываются после **UseRouting** и до **app.UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Вызов **UseAuthentication** регистрирует ПО промежуточного слоя, использующее ранее зарегистрированные схемы проверки подлинности (из **ConfigureServices**). Этот метод должен вызываться до любого ПО промежуточного слоя, зависящего от проверки подлинности пользователей.

### <a name="add-dependencies-and-configure-services"></a>Добавление зависимостей и настройка служб

ASP.NET Core поддерживает шаблон разработки программного обеспечения с [внедрением зависимостей (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1), который является приемом [инверсии управления (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.  

Использование Центра уведомлений и [пакета средств разработки Центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) инкапсулированы в службе. Служба регистрируется и становится доступной через подходящую абстракцию.

1. С нажатой клавишей **CTRL** **щелкните** папку **Dependencies**, а затем выберите **Управление пакетами NuGet...** .

1. Найдите **Microsoft.Azure.NotificationHubs** и убедитесь, что он отмечен.

1. Щелкните элемент **Добавить пакеты**, а затем — элемент **Принять**, когда будет предложено принять условия лицензионного соглашения.

1. **CTRL** + **щелчок** по проекту **PushDemoApi**, затем выберите **Создать папку** в меню **Добавить**, а затем щелкните **Добавить** и укажите *Models* в качестве значения для параметра **Имя папки**.

1. С нажатой клавишей **CTRL** **щелкните** папку **Models**, а затем выберите **Создать файл...** в меню **Добавить**.

1. Выберите **Общие** > **Пустой класс**, введите *PushTemplates.cs* в поле **Имя**, а затем щелкните **Создать**, чтобы добавить следующую реализацию.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс содержит полезные данные уведомления токена для универсальных и автоматических уведомлений, необходимых в этом сценарии. Полезные данные определяются вне папки [Installation](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet), чтобы разрешить эксперименты без необходимости обновлять существующие установки через службу. Обработка изменений в установках таким образом выходит за рамки этого учебника. В рабочей среде рекомендуем использовать [пользовательские шаблоны](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Выберите **Общие** > **Пустой класс**, введите *DeviceInstallation.cs* в поле **Имя** и щелкните **Создать**, чтобы добавить следующую реализацию.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Добавьте еще один **Пустой класс** в папку **Models** с именем *NotificationRequest.cs*, а затем добавьте следующую реализацию.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Добавьте еще один **Пустой класс** в папку **Models** с именем *NotificationHubOptions.cs*, а затем добавьте следующую реализацию.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Добавьте новую папку в проект **PushDemoApi** с именем *Services*.

1. Добавьте **Пустой интерфейс** в папку **Services** и присвойте ему имя *INotificationService.cs*, а затем добавьте следующую реализацию.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Добавьте **Пустой класс** в папку **Services** с именем *NotificationHubsService.cs*, а затем добавьте следующий код, чтобы реализовать интерфейс **INotificationService**:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > Выражение тега, предоставленное для **SendTemplateNotificationAsync**, ограничено 20 тегами. Для большинства операторов ограничение составляет 6, но в данном случае выражение содержит только операторы OR (||). Если в запросе более 20 тегов, они должны быть разделены на несколько запросов. Дополнительные сведения см. в документации [Маршрутизация и выражения тегов](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396).

1. В **Startup.cs** обновите метод **ConfigureServices**, чтобы добавить **NotificationHubsService** в качестве отдельной реализации **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Создание API уведомлений

1. С нажатой клавишей **CTRL** **щелкните** папку **Controllers**, а затем выберите **Создать файл...** в меню **Добавить**.

1. Выберите **ASP.NET Core** > **Web API Controller Class**, введите *NotificationsController* в поле **Имя**, а затем щелкните **Создать**.

1. Добавьте следующие пространства имен в начало файла.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Обновите элемент управления-шаблон, чтобы он наследовался от **ControllerBase** и был снабжен атрибутом **ApiController**.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > Базовый класс **Controller** предоставляет поддержку для представлений, но здесь это не нужно, поэтому вместо него можно использовать **ControllerBase**.

1. Если вы решили пройти раздел [Проверка подлинности клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional), следует также снабдить **NotificationsController** атрибутом **Authorize**.

    ```cs
    [Authorize]
    ```

1. Обновите конструктор, чтобы принять зарегистрированный экземпляр **INotificationService** в качестве аргумента и присвоить его члену с правами только на чтение.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. В **launchSettings.json** (в папке **Properties**) измените **launchUrl** с `weatherforecast` на *api/notifications* в соответствии с URL-адресом, указанным в атрибуте **RegistrationsController** **Route**.

1. Запустите отладку (**Команда** + **Ввод**), чтобы убедиться, что приложение работает с новым **NotificationsController** и возвращает состояние **401 Unauthorized**.

    > [!NOTE]
    > Visual Studio не может автоматически запускать приложение в браузере. Чтобы протестировать API, с этого момента используйте [Postman](https://www.postman.com/downloads).

1. На новой вкладке **[Postman](https://www.postman.com/downloads)** , установите для запроса значение **GET** и введите адрес, указанный ниже.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Локальный адрес должен соответствовать значению **applicationUrl**, найденному в файле **Properties** > **launchSettings.json**. Значение по умолчанию должно быть равно `https://localhost:5001;http://localhost:5000`, однако, если вы получаете ответ 404, убедитесь, что это так.

1. Если вы решили выполнить инструкции в разделе о [проверке подлинности клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional), обязательно настройте заголовки запросов так, чтобы они содержали значение **apikey**.

   | Клавиши                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <ваш_ключ_API>                 |

1. Нажмите кнопку **Отправить**.

    > [!NOTE]
    > Вы должны получить состояние **200 ОК** с содержимым **JSON**.
    >
    > При получении предупреждения **Проверка SSL-сертификата** можно выключить запрос на проверку SSL-сертификата **[Postman](https://www.postman.com/downloads)** в разделе **Параметры**.

1. Замените методы шаблонного класса следующим кодом.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Создание приложения API

Теперь вы создадите [приложение API](https://azure.microsoft.com/services/app-service/api/) в [Службе приложений Azure](https://docs.microsoft.com/azure/app-service/) для размещения серверной службы.  

1. Войдите на [портал Azure](https://portal.azure.com).

1. Щелкните **Создать ресурс**, затем найдите и выберите **Приложение API**, а затем щелкните **Создать**.

1. Обновите перечисленные ниже поля и нажмите кнопку **Создать**.

    **Имя приложения:**  
    Введите глобально уникальное имя для **приложения API**

    **Подписка**:  
    Выберите ту же целевую **Подписку**, в которой создан Центр уведомлений.

    **Группа ресурсов**.  
    Выберите ту же **Группу ресурсов**, в которой создан Центр уведомлений.

    **Расположение или план Службы приложений:**  
    Создайте новый **План Службы приложений**  

    > [!NOTE]
    > Измените значение по умолчанию на план, который поддерживает **SSL**. В противном случае при работе с мобильным приложением понадобится предпринять соответствующие действия, чтобы предотвратить блокировку **HTTP**-запросов.

    **Application Insights:**  
    Примите предлагаемый параметр (новый ресурс будет создан с использованием этого имени) или выберите существующий ресурс.

1. После подготовки **приложения API** перейдите к этому ресурсу.

1. Обратите внимание на свойство **URL** в сводке **Главное** в верхней части раздела **Обзор**. Это URL-адрес *серверной конечной точки*, который будет использоваться далее в этом учебнике.

    > [!NOTE]
    > URL-адрес использует указанное вами ранее имя приложения API в формате `https://<app_name>.azurewebsites.net`.

1. Выберите **Configuration** в списке (в разделе **Параметры**).  

1. Для каждого из приведенных ниже параметров щелкните **Новый параметр приложения**, чтобы ввести **Имя** и **Значение**, а затем нажмите кнопку **ОК**.

   | Имя                               | Значение                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <значение_ключа_API>                |
   | `NotificationHub:Name`             | <значение_имени_центра>               |
   | `NotificationHub:ConnectionString` | <значение_строки_подключения_центра>  |

   > [!NOTE]
   > Это те же параметры, которые были определены ранее в параметрах пользователя. Вы сможете скопировать их. Параметр **Authentication:ApiKey** является обязательным только в том случае, если вы решили завершить раздел [Проверка подлинности клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional). В рабочих сценариях можно использовать [хранилище ключей Azure](https://azure.microsoft.com/services/key-vault). Здесь они были добавлены в качестве параметров приложения, чтобы упростить изложение.

1. После добавления всех параметров приложения нажмите кнопку **Сохранить**, а затем нажмите кнопку **Продолжить**.

### <a name="publish-the-backend-service"></a>Публикация серверной службы

Теперь разверните приложение API, чтобы сделать его доступным для всех устройств.  

1. Измените конфигурацию с **Отладка** на **Выпуск**, если это еще не сделано.

1. **CTRL** + **щелчок** по проекту **PushDemoApi**, а затем выберите **Опубликовать в Azure...** в меню **Публикация**.

1. При появлении запроса выполните проверку подлинности. Используйте учетную запись из предыдущего раздела [Создание приложения API](#create-the-api-app).

1. Выберите **Приложение API Службы приложений Azure**, созданное ранее, в списке в качестве цели публикации, а затем щелкните **Опубликовать**.

После завершения работы мастера он опубликует приложение в Azure, а затем откроет приложение. Запишите **URL-адрес**, если это еще не сделано. Это URL-адрес *серверной конечной точки*, который используется далее в этом учебнике.

### <a name="validating-the-published-api"></a>Проверка опубликованного API

1. В **[Postman](https://www.postman.com/downloads)** откройте новую вкладку, задайте для запроса значение **POST** и введите адрес, показанный ниже. Замените заполнитель основным адресом, который вы записали в предыдущем разделе [Публикация серверной службы](#publish-the-backend-service).

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Основной адрес должен быть в формате ``https://<app_name>.azurewebsites.net/``

1. Если вы решили выполнить инструкции в разделе о [проверке подлинности клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional), обязательно настройте заголовки запросов так, чтобы они содержали значение **apikey**.

   | Клавиши                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <ваш_ключ_API>                 |

1. Выберите значение **raw** (без форматирования) для параметра **Body** (Основной текст), затем выберите **JSON** в списке вариантов форматирования и добавьте содержимое-заполнитель **JSON**.

    ```json
    {}
    ```

1. Нажмите кнопку **Отправить**.

    > [!NOTE]
    > Вы должны получить из службы состояние **400 Bad Request**.

1. Выполните шаги 1-4 еще раз, но на этот раз указав конечную точку запросов для проверки, что вы получаете тот же ответ **400 Bad Request**.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Пока нет возможности проверить API с помощью допустимых данных запроса, так как для этого потребуется информация, относящаяся к платформе, из клиентского мобильного приложения.
