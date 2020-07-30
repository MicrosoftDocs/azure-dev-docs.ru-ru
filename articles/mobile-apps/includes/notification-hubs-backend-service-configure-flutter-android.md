---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 62ff9b49f6b2ba30d3538b2c13812ca7fc1ef583
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401645"
---
### <a name="add-the-google-services-json-file"></a>Добавление JSON-файла сервисов Google

1. Выполните действие **CTRL** + **щелчок** с папкой **android** и выберите **Open in Android Studio** (Открыть в Android Studio). Затем переключитесь на представление **Project** (Проект), если оно еще не открылось.

1. Найдите файл *google-services.json*, который вы скачали ранее при настройке проекта **PushDemo** в [консоли Firebase](https://console.firebase.google.com). Затем перетащите его в корневую папку модуля **app** (**android** > **android** > **app**).

### <a name="configure-build-settings-and-permissions"></a>Настройка параметров сборки и разрешений

1. Переключитесь с представления **Project** (Проект) на представление **Android**.

1. Откройте файл **AndroidManifest.xml**, а затем добавьте разрешения **INTERNET** и **READ_PHONE_STATE** после элемента **application** перед закрывающим тегом **</manifest>** .

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Добавление пакетов SDK для Firebase

1. В **Android Studio** откройте файл уровня проекта **build.gradle** (**Gradle Scripts** > **build.gradle (Project: android)** ) и убедитесь, что в узле ``buildscript`` > **dependencies** есть путь к классу com.google.gms:google-services.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Убедитесь, что при создании **проекта Android**. вы указали последнюю версию в соответствии с инструкциями, приведенными в [консоли Firebase](https://console.firebase.google.com).

1. В файле уровня приложения **build.gradle**(**Gradle Scripts** > **build.gradle (Module: app)** ) примените **подключаемый модуль Gradle сервисов Google**. Примените подключаемый модуль прямо над узлом **android**.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. В том же файле в узле **dependencies** добавьте зависимость для библиотеки Android **Cloud Messaging**.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Убедитесь, что вы ссылаетесь на последнюю версию в соответствии с документацией по [Cloud Messaging для клиента Android](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Сохраните изменения, а затем нажмите кнопку **Sync Now** (Синхронизировать сейчас) (в запросе панели инструментов) или **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle).

### <a name="handle-push-notifications-for-android"></a>Работа с push-уведомлениями для Android

1. В **Android Studio** выполните действие **CTRL** + **щелчок** с папкой пакета **com.<ваша_организация>.pushdemo** (**app** > **src** > **main** > **kotlin**) и выберите в меню **New** (Создать) пункт **Package** (Пакет). Введите **services** в качестве имени, а затем щелкните **Return** (Вернуть).

1. Выполните действие**CTRL** + **щелчок** с папкой **services** и выберите в меню **New** (Создать) пункт **Kotlin File/Class** (Класс или файл Kotlin). Введите **DeviceInstallationService** в качестве имени, а затем щелкните **Return** (Вернуть).

1. Реализуйте **DeviceInstallationService** с помощью следующего кода.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/deviceinstallation` для конкретной платформы. Это было определено в части Flutter приложения в файле **DeviceInstallationService.dart**. В этом случае вызовы выполняются из общего кода на собственном узле. Не забудьте изменить **<ваша_организация>** именем своей организации во всех вхождениях.
    >
    > Этот класс предоставляет уникальный идентификатор (с использованием [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) в составе полезных данных при регистрации в центре уведомлений.

1. Добавьте в папку **services** еще один **класс файла Kotlin** с именем *NotificationRegistrationService*, а затем добавьте следующий код.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/notificationregistration` для конкретной платформы. Это было определено в части Flutter приложения в файле **NotificationRegistrationService.dart**. В этом случае вызовы выполняются из собственного узла в общем коде. Не забудьте заменить **<ваша_организация>** именем своей организации во всех вхождениях.

1. Добавьте в папку **services** еще один **класс или файл Kotlin** с именем *NotificationActionService*, а затем добавьте следующий код.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс реализует аналог канала `com.<your_organization>.pushdemo/notificationaction` для конкретной платформы. Это было определено в части Flutter приложения в файле **NotificationActionService.dart**. В этом случае вызовы могут выполняться в обоих направлениях. Не забудьте изменить **<ваша_организация>** именем своей организации во всех вхождениях.

1. Добавьте в пакет **com.<ваша_организация>.pushdemo** новый **класс или файл Kotlin** с именем *PushNotificationsFirebaseMessagingService*, а затем реализуйте его с помощью следующего кода.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Этот класс отвечает за обработку уведомлений при выполнении приложения на переднем плане. Он будет вызывать **triggerAction** в **NotificationActionService**, если действие включено в полезные данные уведомлений, получаемых в **onMessageReceived**. Он также будет вызывать **refreshRegistration** в **NotificationRegistrationService** при повторном создании маркера **Firebase**, переопределив функцию **onNewToken**.
    >
    > Не забудьте заменить **<ваша_организация>** именем своей организации во всех вхождениях.

1. В файле **AndroidManifest.xml** (**app** > **src** > **main**) добавьте **PushNotificationsFirebaseMessagingService** в нижнюю часть элемента **application** с фильтром намерения `com.google.firebase.MESSAGING_EVENT`.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. Вернитесь в **DeviceInstallationService** и убедитесь, что в верхней части файла указаны следующие инструкции import.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Замените **<ваша_организация>** именем своей организации.

1. Обновите текст заполнителя *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation*, чтобы получить значение маркера из **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. В **MainActivity** убедитесь, что в верхней части файла указаны следующие инструкции import.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Замените **<ваша_организация>** именем своей организации.

1. Добавьте переменную для хранения ссылки на **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Добавьте функцию с именем **processNotificationActions**, чтобы проверить наличие в **Intent** дополнительного значения с именем **action**. Условно активируйте это действие или сохраните его для дальнейшего использования, если это действие обрабатывается во время запуска приложения.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Переопределите функцию **onNewIntent**, чтобы она вызывала метод **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Так как параметр **LaunchMode** для **MainActivity** имеет значение **SingleTop**, объект **Intent** будет отправляться в существующий экземпляр **Activity** с помощью функции **onNewIntent**, а не функции **onCreate**. Это означает, что вам необходимо обрабатывать входящий объект **Intent** в обоих функциях: **onCreate** и **onNewIntent**.

1. Переопределите функцию **onCreate** и задайте для **deviceInstallationService** новый экземпляр **DeviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Задайте свойства **notificationActionService** и **notificationRegistrationService** в **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. В той же функции условно вызовите **FirebaseInstanceId.getInstance().instanceId**. Реализуйте **OnCompleteListener**, чтобы задать результирующее значение **token** в **PushNotificationFirebaseMessagingService**, прежде чем вызывать **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. По-прежнему находясь в **onCreate**, вызовите **processNotificationActions** в конце функции. Используйте значение *true* для аргумента *launchAction*, чтобы указать, что это действие обрабатывается во время запуска приложения.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Приложение необходимо регистрировать повторно при каждом запуске и остановке из сеанса отладки, чтобы и по-прежнему получать push-уведомления.
