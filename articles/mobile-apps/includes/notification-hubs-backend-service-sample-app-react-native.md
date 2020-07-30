---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: ef5f71997eca3d3ac35e004b5ee97668fb7582c9
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401665"
---
### <a name="create-the-react-native-solution"></a>Создание решения React Native

1. В сеансе `Terminal` обновите средства среды, необходимые для работы с React Native, выполнив следующие команды.

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. В сеансе `Terminal` выполните следующую команду, чтобы удалить интерфейс командной строки `React Native`, если он установлен. Используйте `npx`, чтобы автоматически получить доступ к последней доступной версии React Native CLI:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native имеет встроенный интерфейс командной строки. Чтобы не устанавливать и не администрировать определенную версию CLI глобально, мы рекомендуем во время выполнения получить текущую версию с помощью `npx` (поставляется с Node.js). При использовании `npx react-native <command>` автоматически скачивается и выполняется последняя на момент выполнения команды стабильная версия CLI.

1. Перейдите к папке проектов, в которой нужно создать новое приложение. Используйте шаблон на основе Typescript, указав параметр `--template`.

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Запустите сервер metro, который собирает пакеты JavaScript и отслеживает обновления кода для обновления пакетов в режиме реального времени.

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Запустите приложение iOS, чтобы проверить установку. Прежде чем выполнять следующую команду, обязательно запустите симулятор iOS или подключите реальное устройство iOS.

    ```bash
    npx react-native run-ios
    ```

1. Запустите приложение Android, чтобы проверить установку. Чтобы эмулятор или устройство Android получили доступ к серверу metro для React Native, требуется выполнить несколько дополнительных действий. Следующие команды создают исходный пакет JavaScript для Android и помещают его в папку assets.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Этот скрипт будет предварительно развернут с первоначальной версией приложения. Когда развертывание завершится, настройте в эмуляторе или на устройстве доступ к серверу metro, указав его IP-адрес. Выполните сборку приложения Android и запустите его с помощью следующей команды:

    ```bash
    npx react-native run-android
    ```

    Открыв приложение, нажмите клавиши `CMD+M` в эмуляторе или встряхните устройство, чтобы заполнить параметры разработчика, затем перейдите к `Settings` > `Change Bundle Location` и укажите IP-адрес сервера metro с портом по умолчанию: `<metro-server-ip-address>:8081`.

1. В файле `App.tsx` примените все нужные изменения макета страницы, сохраните их и убедитесь, что они автоматически учитываются в приложениях iOS и Android.

    > [!NOTE]
    > Подробное руководство по настройке среды разработки можно найти в [официальной документации.](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Установка необходимых пакетов

Для этого примера необходимы следующие три пакета:

1. [Push-уведомления iOS для React Native](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [проект GitHub](https://github.com/react-native-community/push-notification-ios)

    Этот пакет был создан при отделении PushNotificationIOS от ядра React Native. Он реализует собственный механизм iOS для push-уведомления и предоставляет в React Native интерфейс для работы с ним. Выполните следующую команду, чтобы установить этот пакет.

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Кросс-платформенные push-уведомления для React Native](https://www.npmjs.com/package/react-native-push-notification)

    Этот пакет реализует локальные и удаленные уведомления в iOS и Android, используя кросс-платформенный подход. Выполните следующую команду, чтобы установить этот пакет.

    ```bash
    yarn add react-native-push-notification
    ```

1. [Пакет сведений об устройстве](https://www.npmjs.com/package/react-native-device-info) Этот пакет предоставляет сведения об устройстве в среде выполнения. Используйте его, чтобы определить идентификатор устройства, который используется при регистрации для получения push-уведомлений. Выполните следующую команду, чтобы установить этот пакет.

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Реализация кроссплатформенных компонентов

1. Создайте и реализуйте `DemoNotificationHandler`.

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Создайте и реализуйте `DemoNotificationService`.

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Создайте и реализуйте `DemoNotificationRegistrationService`.

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Настройте приложение. Откройте `package.json` и добавьте следующее определение скрипта.

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Теперь выполните этот скрипт, который копирует конфигурацию по умолчанию в папку `config`.

    ```bash
    yarn configure
    ```

    Последним шагом является внесение сведений о доступе к API в файл конфигурации, который вы скопировали на предыдущем шаге. Укажите параметры `apiKey` и `apiUrl`.

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Реализация кросс-платформенного пользовательского интерфейса

1. Определение макета страницы

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Применение стилей

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Инициализация компонентов страницы

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Определение обработчиков щелчков для кнопки

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Обработка регистрации полученных маркеров и push-уведомлений

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```