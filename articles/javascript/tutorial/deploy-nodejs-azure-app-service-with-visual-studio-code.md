---
title: Развертывание приложений Node.js в Службе приложений Azure с помощью Visual Studio Code
description: Разверните приложение Node.js (Express.js) в Службе приложений Azure с помощью расширения Службы приложений для Visual Studio Code.
ms.topic: tutorial
ms.date: 01/11/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2c019cc9ae13b81ecde934faee6d7d7a9fadf07a
ms.sourcegitcommit: 657f43a5048cd17b080b40b5090d575c8d7f5eaf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98173254"
---
# <a name="deploy-nodejs-to-azure-app-service-using-visual-studio-code"></a>РазвертываниеNode.js в Службе приложений Azure с помощью Visual Studio Code

Разверните приложение Node.js в Службе приложений Azure (в Linux или Windows) с помощью [расширения Службы приложений](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для Visual Studio Code.

Разверните приложение Node.js в Azure с помощью Git и расширения Службы приложений Azure. Для этого нужно выполнить следующие задачи:

* создание приложения Express.js;
* инициализация локального репозитория Git;
* создание ресурса веб-приложения для размещения приложения;
* развертывание приложения в ресурсе;
* локальный просмотр удаленных журналов.

## <a name="walkthrough-video"></a>Пошаговое видеоруководство

Просмотрите это видео, чтобы ознакомиться с полным пошаговым руководством по содержимому этой статьи.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-to-Azure-App-Service-using-Visual-Studio-Code/player]

## <a name="1-set-up-your-development-environment"></a>1. Настройка среды разработки

- Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для VS Code (установленное в VS Code).
- [Node.js 12 и более поздних версий и npm](https://nodejs.org/en/download).

## <a name="2-sign-in-to-azure"></a>2. Вход в Azure

[!INCLUDE [azure-sign-in](../includes/azure-sign-in.md)]

## <a name="3-create-a-local-expressjs-app"></a>3. Создание локального приложения Express.js

[!INCLUDE [Create a local Express.js app](../includes/create-node-app.md)]

## <a name="4-run-your-local-expressjs-app"></a>4. Запуск локального приложения Express.js

[!INCLUDE [Run your local Express.js app](../includes/run-node-app.md)]

## <a name="5-initialize-git-in-visual-studio-code-for-current-app"></a>5. Инициализация Git в Visual Studio Code для текущего приложения

1. В окне терминала перейдите к папке *expressApp1*, а затем запустите Visual Studio Code с помощью следующей команды:

    ```bash
    code .
    ```

1. В Visual Studio Code выберите значок системы управления версиями, чтобы открыть обозреватель **системы управления версиями**. Затем щелкните элемент **Инициализировать репозиторий**, чтобы инициализировать локальный репозиторий Git:

    ![Инициализация репозитория Git](../media/deploy-azure/git-init.png)

1. Когда будет предложено, выберите *expressApp1* в качестве папки рабочей области.

1. После инициализации репозитория введите сообщение Initial commit (Начальная фиксация) и установите флажок, чтобы создать исходную фиксацию исходных файлов.

    ![Выполнение исходной фиксации в репозиторий](../media/deploy-azure/initial-commit.png)

## <a name="6-create-app-service-resource-in-visual-studio-code"></a>6. Создание ресурса Службы приложений в Visual Studio Code

1. В палитре команд (**CTRL**+**SHIFT**+**P**) введите create web и выберите **Служба приложений Azure: Создание нового веб-приложения > Дополнительно**. Используйте команду advanced для полного управления развертыванием, в том числе группой ресурсов, планом Службы приложений и операционной системой, чтобы не использовать значения по умолчанию для Linux.

1. Ответьте на запросы следующим образом:

    - Выберите учетную запись своей **подписки**.
    - В поле **Введите глобально уникальное имя** введите уникальное имя в пределах среды Azure. Используйте только буквенно-цифровые символы (A–Z, a–z и 0–9) и дефисы (-).
    - Выберите **Создать группу ресурсов** и укажите имя, например `AppServiceTutorial-rg`.
    - Выберите операционную систему (Windows или Linux).
    - Для Linux выберите версию Node.js. Для Windows вы укажете версию позже с помощью параметра приложения.
    - Выберите элемент **Создать новый план служб приложений**, укажите имя (например, `AppServiceTutorial-plan`) и выберите [ценовую категорию](../core/what-is-azure-for-javascript-development.md#free-tier-resources) **F1 Free** (Бесплатный F1).
    - Выберите **Пропустить** для ресурса Application Insights.
    - Выберите ближайшее расположение.

1. Через некоторое время в VS Code появится уведомление о завершении создания. Закройте уведомление с помощью кнопки **X**.

    ![Уведомление о завершении создания веб-приложения](../media/deploy-azure/creation-complete.png)

1. Когда вы создадите веб-приложение, разверните в VS Code код из локального репозитория Git. Щелкните значок Azure, чтобы открыть обозреватель **службы приложений Azure**, разверните узел подписки, щелкните правой кнопкой мыши имя только что созданного веб-приложения и выберите **Настроить источник развертывания**.

    ![Команда настройки источника развертывания для веб-приложения](../media/deploy-azure/configure-deployment-source.png)

1. При появлении запроса выберите **LocalGit**.

1. Если вы развертываете приложение в Службе приложений в Windows, потребуется создать два параметра перед развертыванием.

    1. В VS Code разверните узел для новой службы приложений, щелкните правой кнопкой мыши **Параметры приложения** и выберите **Добавить новый параметр**.

        ![Команда добавления параметра приложения](../media/deploy-azure/add-setting.png)

    1. Введите `WEBSITE_NODE_DEFAULT_VERSION` для ключа параметра и `10.15.2` в качестве значения параметра. Этот параметр позволяет задать версию Node.js.
    1. Повторите эту процедуру, чтобы создать ключ для `SCM_DO_BUILD_DURING_DEPLOYMENT` со значением `1`. Этот параметр позволяет принудительно запустить `npm install` на сервере при развертывании.
    1. Разверните узел **Параметры приложения**, чтобы проверить, установлены ли параметры.

1. Щелкните значок с синей стрелкой вверх, чтобы развернуть код в Azure:

    ![Значок "Развернуть в веб-приложении"](../media/deploy-azure/deploy.png)

1. Когда будет предложено, выберите папку *expressApp1*, учетную запись **подписки** и имя созданного ранее веб-приложения.

1. При развертывании в Linux нажмите кнопку **Да**, когда появится запрос на обновление конфигурации, чтобы выполнить `npm install` на целевом сервере.

    ![Запрос на обновление конфигурации на целевом сервере Linux](../media/deploy-azure/server-build.png)

1. Выберите **Да** при появлении запроса **Всегда развертывать рабочую область nodejs-docs-hello-world в (имя приложения)** . Если выбрать **Да**, VS Code будет в дальнейшем автоматически выполнять развертывания в том же веб-приложении Службы приложений.

1. По завершении развертывания щелкните **Обзор веб-сайта** в диалоговом окне с предложением просмотреть только что развернутое веб-приложение. В браузере должно отобразиться сообщение Hello World!.

1. (Необязательно.) Вы можете внести изменения в файлы кода, а затем снова нажать кнопку развертывания, чтобы обновить веб-приложение.

## <a name="7-stream-remote-service-logs-in-visual-studio-code"></a>7. Потоковая передача журналов удаленной службы в Visual Studio Code

Просматривайте любые выходные данные или выводите последний фрагмент данных, которые выполняющееся приложение создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Просмотр журналов потоковой передачи](../media/deploy-azure/start-streaming-logs.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение.

    ![Запрос на включение ведения журналов и перезапуск](../media/deploy-azure/enable-restart.png)

1. После перезапуска приложения откроется окно **Вывод** в VS Code с подключением к потоку журнала для вывода данных.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Несколько раз обновите веб-страницу в браузере и убедитесь, что отображаются дополнительные выходные данные журнала.

## <a name="8-make-changes-and-redeploy"></a>8. Внесение изменений и повторное развертывание

Внесите несколько изменений и [повторно разверните](../how-to/deploy-web-app.md#deploy-or-redeploy-to-app-service-with-visual-studio-code) приложение с помощью расширения Службы приложений. 

## <a name="9-clean-up-resources"></a>9. Очистка ресурсов

Если нужно очистить ресурсы, щелкните правой кнопкой мыши Службу приложений в расширении Службы приложений Visual Studio Code, а затем выберите элемент **Удалить**.

:::image type="content" source="../media/deploy-azure/delete-azure-app-service-with-visual-studio-code-extension.png" alt-text="Если нужно очистить ресурсы, щелкните правой кнопкой мыши Службу приложений в расширении Службы приложений Visual Studio Code, а затем выберите элемент **Удалить**.":::

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как настроить параметры своего приложения](../how-to/configure-web-app-settings.md)

[!INCLUDE [tutorial-next-steps](../includes/tutorial-next-steps.md)]
