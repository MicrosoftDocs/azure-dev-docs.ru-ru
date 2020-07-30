---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6a26b6e6f40ae4cb705799a2d49f2f62904c08c2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401625"
---
### <a name="create-the-flutter-solution"></a>Создание решения Flutter

1. Откройте новый экземпляр **Visual Studio Code**.

1. Откройте **палитру команд** (**SHIFT** + **COMMAND** + **P**).

1. Выберите команду **Flutter: New Project** (Flutter: создать проект) и нажмите клавишу **ВВОД**.

1. Введите *push_demo* в поле **Project Name** (Имя проекта) и выберите **расположение проекта**.

1. При появлении запроса выберите **Get Packages** (Получить пакеты).

1. Выполните действие **CTRL** + **щелчок** с папкой **kotlin** (в папке **app** > **src** > **main**), а затем выберите **Reveal in Finder** (Показать в средстве поиска). Затем переименуйте дочерние папки (в папке **kotlin**) на ```com```, ```<your_organization>``` и ```pushdemo``` соответственно.

    > [!NOTE]
    > При использовании шаблона **Visual Studio Code** эти папки по умолчанию принимают имена **com**, **example**, **<project_name>** . Если для **организации** используется значение **mobcat**, структура папок будет выглядеть следующим образом:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. В **Visual Studio Code** измените значение **applicationId** в файле **android** > **app** > **build.gradle** на `com.<your_organization>.pushdemo`.

    > [!NOTE]
    > Вместо заполнителя *<your_organization>* укажите имя своей организации. Например, использование **mobcat** в качестве организации приведет к изменению **имени пакета** на *com.mobcat.pushdemo*.

1. Измените атрибут **package** в файлах **AndroidManifest.xml** в папках **src** > **debug**, **src** > **main** и **src** > **profile** соответственно. Убедитесь, что значения соответствуют значению **applicationId**, используемому на предыдущем шаге.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Измените значение атрибута ```android:label``` в файле **AndroidManifest.xml** в папке **src** > **main** на *PushDemo*. Затем добавьте атрибут ```android:allowBackup``` непосредственно под ```android:label``` и задайте для него значение **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Откройте файл уровня приложения **build.gradle** (**android** > **app** > **build.gradle**), а затем измените *compileSdkVersion* (из раздела **android**) для использования **29** API. Затем измените значения *minSdkVersion* и *targetSdkVersion* (из раздела **defaultConfig**) на **26** и **29** соответственно.

    > [!NOTE]
    > Для задач, выполняемых в этом руководстве, поддерживаются только устройства с **API уровня 26 и выше**, но вы можете расширить поддержку до устройств с более ранними версиями API.

1. Выполните действие **CTRL** + **щелчок** с папкой **ios** и выберите **Open in Xcode** (Открыть в Xcode).

1. В **Xcode** щелкните **Runner** (Средство выполнения) (**xcodeproj** вверху, а не папка). Затем выберите целевой объект для **средства выполнения** и откройте вкладку **General** (Общие). Выберите **All** (Все) для конфигурации сборки и измените значение параметра **Bundle Identifier** (Идентификатор пакета) на `com.<your_organization>.PushDemo`.

    > [!NOTE]
    > Вместо заполнителя *<your_organization>* укажите имя своей организации. Например, использование **mobcat** в качестве организации приведет к изменению значения **идентификатора пакета** на *com.mobcat.PushDemo*.

1. Щелкните **Info.plist** и измените значение параметра **Bundle name** (Имя пакета) на **PushDemo**.

1. Закройте **Xcode** и вернитесь в **Visual Studio Code**.

1. В **Visual Studio Code** откройте файл **pubspec.yaml** и добавьте **пакеты Dart** [http](https://pub.dev/packages/http) и [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) в качестве зависимостей. Сохраните файл и щелкните **Get Packages** (Получить пакеты) при появлении запроса.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. В окне **Terminal** (Терминал) измените каталог на папку **ios** (для вашего проекта Flutter). Затем выполните команду [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation), чтобы установить новые группы pod (требуются для пакета [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)).

1. Выполните действие **CTRL** + **щелчок** с папкой **lib**, а затем выберите **New File** (Создать файл) в меню, указав *main_page.dart* в качестве имени файла. Затем добавьте следующий код.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. В файле **main.dart** замените код шаблона следующим кодом.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. В окне **Terminal** (Терминал) создайте и выполните приложение на каждой целевой платформе, чтобы протестировать шаблонное приложение, выполняющееся на ваших устройствах. Убедитесь, что поддерживаемые устройства подключены.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Реализация кроссплатформенных компонентов

1. Выполните действие **CTRL** + **щелчок** с папкой **lib**, а затем выберите **New Folder** (Создать папку) в меню, указав *models* в качестве **имени папки**.

1. Выполните действие **CTRL** + **щелчок** с папкой **models**, а затем выберите **New File** (Создать файл) в меню, указав *device_installation.dart* в качестве имени файла. Затем добавьте следующий код.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Добавьте новый файл в папку **models** с именем *push_demo_action.dart* для определения перечисления действий, которые поддерживаются в этом примере.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Добавьте новую папку в проект с именем **services**, а затем добавьте новый файл в папку с именем *device_installation_service.dart* со следующей реализацией.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Вместо заполнителя *<your_organization>* укажите имя своей организации. Например, использование **mobcat** в качестве организации приведет к изменению [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) на *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Это класс инкапсулирует процессы для работы с базовой нативной платформой для получения обязательных сведений об установке устройства. Класс [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) обеспечивает двунаправленный асинхронный обмен данными с базовыми нативными платформами. Соответствующий компонент конкретной платформы для этого канала будет создан далее.

1. Добавьте еще один файл в папку с именем *notification_action_service.dart* и следующей реализацией.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Он используется в качестве простого механизма для централизации кросс-платформенной обработки действий уведомлений с использованием строго типизированного перечисления. Служба позволяет базовой нативной платформе запускать действие, если оно определено в полезных данных уведомления. Она также позволяет общему коду ретроспективно выполнять проверку того, было ли определено действие во время запуска приложения на момент готовности Flutter обработать его. Например, если приложение было запущено путем выбора уведомления в центре уведомлений.

1. Добавьте новый файл в папку **services** с именем *notification_registration_service.dart* и следующей реализацией.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Этот класс инкапсулирует использование **DeviceInstallationService** и отправляет запрос к серверной службе для выполнения обязательных действий регистрации, отмены регистрации и обновления. Аргумент **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).

1. Добавьте новый файл в папку **lib** с именем *config.dart* и следующей реализацией.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Он используется в качестве простого способа для определения секретов приложений. Замените заполнители реальными значениями для конечной точки. Вы должны были записать их при создании внутренней службы. **URL-адрес приложения API** должен иметь вид `https://<api_app_name>.azurewebsites.net/`. Элемент **apiKey** является обязательным, только если вы будете выполнять инструкции из раздела [Аутентификация клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional).
    >
    > Обязательно добавьте его в свой файл gitignore, чтобы избежать фиксации этих секретов в системе управления версиями.

### <a name="implement-the-cross-platform-ui"></a>Реализация кросс-платформенного пользовательского интерфейса

1. В файле **main_page.dart** замените функцию **build** следующим кодом.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Добавьте необходимые инструкции import в файл **main_page.dart**.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Добавьте поле в класс **_MainPageState**, чтобы сохранить ссылку на **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. В классе **_MainPageState** реализуйте обработчики событий для событий **onPressed** кнопок **Register** и **Deregister**. Вызовите соответствующие методы **Register**/**Deregister**, а затем отобразите оповещение для указания результата.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Теперь в файле **main.dart** убедитесь, что в верхней части файла указаны следующие инструкции import.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Объявите переменную для хранения ссылки на экземпляр **NotificationActionService** и инициализируйте ее.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Добавьте функции для управления отображением оповещения при активации действия.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Обновите функцию **main** для наблюдения потока **actionTriggered** в **NotificationActionService** и проверьте наличие действий, зарегистрированных во время запуска приложения.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Здесь мы просто демонстрируем получение и распространение действий push-уведомлений. Обычно вместо отображения оповещения в этом случае выполняется автоматическая обработка, например переход к конкретному представлению или обновление некоторых данных.
