---
title: Приложение Express.js преобразует текст в речь с помощью службы "Речь" из Cognitive Services
description: Используйте службу "Речь" из Cognitive Services, чтобы преобразовать текст в речь, как показано на примере клиента и сервера.
ms.topic: tutorial
ms.date: 01/20/2021
ms.custom: languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 028871f55a7be2e38191efef37a65ad41ab9c3dc
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117888"
---
# <a name="expressjs-app-converts-text-to-speech-with-cognitive-services-speech"></a>Приложение Express.js преобразует текст в речь с помощью службы "Речь" из Cognitive Services

В этом руководстве описано, как добавить службу "Речь" из Cognitive Services в существующее приложение Express.js, чтобы реализовать в нем преобразование из текста в речь с помощью этой службы. Преобразование текста в речь позволяет выводить аудио без затрат на его генерирование вручную. 

В этом руководстве приведено три разных способа преобразования текста в речь с помощью службы "Речь" из Azure Cognitive Services:

* Клиент JavaScript получает аудио напрямую. 
* Серверный код JavaScript получает аудио из файла (*.MP3).
* Серверный код JavaScript получает аудио из arrayBuffer в памяти.

## <a name="application-architecture"></a>Архитектура приложения

В этом руководстве описано, как создать простое приложение Express.js и добавить функциональные возможности с помощью таких сочетаний:

* новый маршрут для API сервера для преобразования текста в речь с результирующим потоком в формате MP3;
* новый маршрут для HTML-формы, позволяющей ввести данные;
* новая HTML-форма с JavaScript, обеспечивающая вызов к службе "Речь" на стороне клиента.

Это приложение предоставляет три разных вызова для преобразования текста в речь:

* Первый вызов сервера создает файл на сервере и возвращает его клиенту. Обычно этот вызов используется для более длинного текста или текста, который нужно озвучить более одного раза. 
* Второй вызов сервера предназначен для более короткого текста и сохраняется в памяти, прежде чем он будет возвращен клиенту. 
* Вызов клиента демонстрирует прямой вызов к службе "Речь" с использованием пакета SDK. Этот вызов подходит в тех случаях, если у вас есть клиентское приложение без сервера. 

## <a name="prerequisites"></a>Предварительные требования


- [Node.js 10.1+ и npm](https://nodejs.org/en/download), установленные на локальном компьютере.
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
- [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для VS Code (установленное в VS Code).
- Средство [Git](https://git-scm.com/downloads) для отправки данных в GitHub, по которым активируется действие GitHub.
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) можно использовать в Bash [![встроенный запуск](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)   
- При необходимости [установите](/cli/azure/install-azure-cli) Azure CLI, чтобы выполнять справочные команды CLI.
   - Если вы используете локальную установку, выполните вход с помощью команды Azure CLI [az login](/cli/azure/reference-index#az-login).  Чтобы выполнить аутентификацию, следуйте инструкциям в окне терминала.  Сведения о дополнительных вариантах входа см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).
  - Если появится запрос, установите расширения Azure CLI при первом использовании.  Дополнительные сведения о расширениях см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Выполните команду [az version](/cli/azure/reference-index?#az_version), чтобы узнать установленную версию и зависимые библиотеки. Чтобы обновиться до последней версии, выполните команду [az upgrade](/cli/azure/reference-index?#az_upgrade).

## <a name="download-sample-expressjs-repo"></a>Скачивание примера репозитория Express.js 

1. С помощью Git клонируйте пример репозитория Express.js на локальный компьютер. 

    ```bash
    git clone https://github.com/Azure-Samples/js-e2e-express-server
    ```

1. Перейдите в новый каталог для примера.

    ```bash
    cd js-e2e-express-server
    ```

1. Откройте проект в Visual Studio Code.

    ```bash
    code .
    ```

1. Откройте новый терминал в Visual Studio Code и установите зависимости проекта.

    ```bash
    npm install
    ```

## <a name="install-cognitive-services-speech-sdk-for-javascript"></a>Установка пакета SDK службы "Речь" из Cognitive Services для JavaScript

В терминале Visual Studio Code установите пакет SDK службы "Речь" из Azure Cognitive Services.

```bash
npm install microsoft-cognitiveservices-speech-sdk
```

## <a name="create-a-speech-module-for-the-expressjs-app"></a>Создание модуля службы "Речь" для приложения Express.js

1. Чтобы интегрировать пакет SDK службы "Речь" с приложением Express.js, создайте файл `azure-cognitiveservices-speech.js` в папке `src`.
1. Добавьте следующий код для извлечения зависимостей и создайте функцию для преобразования текста в речь.

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/azure-cognitiveservices-speech.js" highlight="3,21,32" :::

    * Параметры — файл извлекает зависимости для использования пакета SDK, потоков, буферов и файловой системы. Функция `textToSpeech` принимает четыре аргумента. Если отправляется имя файла с локальным путем, текст преобразуется в звуковой файл. Если имя файла не отправляется, создается аудиопоток в памяти. 
    * Метод в пакете SDK службы "Речь" — метод [synthesizer.speakTextAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#speakTextAsync_string___e__SpeechSynthesisResult_____void___e__string_____void__AudioOutputStream___PushAudioOutputStreamCallback___PathLike_) возвращает различные типы в зависимости от полученной конфигурации. 
        Метод возвращает результат, который зависит от того, что должен был сделать метод:
        * Создать файл 
        * Создать поток в памяти в виде массива буферов.
    * Формат аудио — для аудио выбран формат MP3, но существуют и [другие форматы](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest), а также другие [методы настройки аудио](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest#methods). 

    Локальный метод `textToSpeech` упаковывает и преобразует функцию обратного вызова пакета SDK в обещание. 

## <a name="create-a-new-route-for-the-expressjs-app"></a>Создание нового маршрута для приложения Express.js

1. Откройте файл `src/server.js`. 
1. Добавьте модуль `azure-cognitiveservices-speech.js` в качестве зависимости в начале файла:

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="3"  :::
    

1. Добавьте новый маршрут API для вызова метода **textToSpeech**, создание которого описано в предыдущем разделе руководства. 

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="30-51" highlight="45-50" :::

    Этот метод принимает обязательные и необязательные параметры для метода `textToSpeech` из строки запроса. Если требуется создать файл, генерируется уникальное имя файла. Метод `textToSpeech` вызывается асинхронно и передает результат в объект ответа (`res`). 

## <a name="update-the-client-web-page-with-a-form"></a>Обновление веб-страницы клиента с помощью формы 

Обновите веб-страницу клиента в формате HTML с помощью формы, которая собирает обязательные параметры. Необязательный параметр передается в зависимости от того, какой элемент управления звуком выбрал пользователь. Поскольку в этом руководстве предоставляется механизм для вызова службы "Речь" Azure из клиента, также предоставляется код JavaScript. 

Откройте файл `/public/client.html` и замените имеющееся содержимое следующим кодом:

:::code language="html" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/public/client.html" highlight="75, 102 137" :::

Выделенные строки в файле: 

* Строка 74: пакет SDK службы "Речь" извлекается в клиентскую библиотеку с помощью сайта `cdn.jsdelivr.net` для доставки пакета npm. 
* Строка 102: метод `updateSrc` обновляет URL-адрес папки `src` элементов управления аудио с помощью строки запроса (включая ключ, регион и текст). 
* Строка 137: если пользователь нажимает кнопку `Get directly from Azure`, веб-страница обращается напрямую к Azure со страницы клиента и обрабатывает результат. 

## <a name="create-cognitive-services-speech-resource"></a>Создание ресурса службы "Речь" из Cognitive Services

Создайте ресурс службы "Речь" с помощью команд Azure CLI в Azure Cloud Shell.


1. Войдите в [Azure Cloud Shell](https://shell.azure.com). Для этого необходимо пройти аутентификацию в браузере с учетной записью, имеющей разрешение на действительную подписку Azure. 
1. Создайте группу ресурсов для ресурса службы "Речь". 

    ```azurecli
    az group create \
        --location eastus \
        --name tutorial-resource-group-eastus
    ```

1. Создайте ресурс службы "Речь" в группе ресурсов.

    ```azurecli
    az cognitiveservices account create \
        --kind SpeechServices \
        --location eastus \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --sku F0
    ```

    Эта команда завершится ошибкой, если ваш бесплатный ресурс службы "Речь" уже создан. 

1. Используйте команду, чтобы получить значения ключей для нового ресурса службы "Речь". 

    ```azurecli
    az cognitiveservices account keys list \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --output table
    ```

1. Скопируйте один из ключей. 

    Ключ в веб-форме используется для аутентификации в службе "Речь" в Azure.

## <a name="run-the-expressjs-app-to-convert-text-to-speech"></a>Запуск приложения Express.js для преобразования текста в речь

1. Запустите приложение с помощью следующей команды Bash.

    ```bash
    npm start
    ```

1. Откройте веб-приложение в браузере.

    ```
    http://localhost:3000    
    ```

1. Вставьте свой ключ службы "Речь" в выделенное текстовое поле. 

    :::image type="content" source="../media/speech-tutorial/expressjs-webapp-form-with-speech-key-field-highlighted.png" alt-text="Снимок экрана браузера с веб-формой и выделенным полем для ввода ключа службы &quot;Речь&quot;.":::

1. При необходимости измените текст на другой. 

1. Чтобы начать преобразование в аудиоформат, нажмите одну из трех кнопок:
    * Получить напрямую из Azure — используется вызов на стороне клиента в Azure.
    * Элемент управления аудио для получения аудио из файла.
    * Элемент управления аудио для получения аудио из буфера.

    Вы можете заметить небольшую задержку между выбором элемента управления и воспроизведением аудио. 

## <a name="create-new-azure-app-service-in-visual-studio-code"></a>Создание Службы приложений Azure в Visual Studio Code

1. В палитре команд (**CTRL**+**SHIFT**+**P**) введите create web и выберите **Служба приложений Azure: Создание нового веб-приложения > Дополнительно**. Используйте команду advanced для полного управления развертыванием, в том числе группой ресурсов, планом Службы приложений и операционной системой, чтобы не использовать значения по умолчанию для Linux.

1. Ответьте на запросы следующим образом:

    - Выберите учетную запись своей **подписки**.
    - В поле **Введите глобально уникальное имя** (например, `my-text-to-speech-app`): 
        - введите имя, уникальное в пределах Azure. Используйте только буквенно-цифровые символы (A–Z, a–z и 0–9) и дефисы (-).
    - Выберите `tutorial-resource-group-eastus` для группы ресурсов.
    - **Выберите стек среды выполнения** `Node 10.1` или более новой версии. 
    - Выберите операционную систему Linux.
    - Выберите элемент **Создать новый план служб приложений**, укажите имя (например, `my-text-to-speech-app-plan`) и выберите [ценовую категорию](../core/what-is-azure-for-javascript-development.md#free-tier-resources) **F1 Free** (Бесплатный F1).
    - Выберите бесплатную ценовую категорию **F1**.
    - Выберите **Пропустить** для ресурса Application Insights.
    - Выберите расположение `eastus`. 

1. Через некоторое время в Visual Studio Code появится уведомление о завершении создания. Закройте уведомление с помощью кнопки **X**.

## <a name="deploy-local-expressjs-app-to-remote-app-service-in-visual-studio-code"></a>Развертывание локального приложения Express.js в удаленной Службе приложений в Visual Studio Code

1. Так как веб-приложение уже готово, разверните код с локального компьютера. Щелкните значок Azure, чтобы открыть обозреватель **Службы приложений Azure**, разверните узел подписки, щелкните правой кнопкой мыши имя только что созданного веб-приложения и выберите **Развернуть в веб-приложении**.

1. Когда будет предложено, выберите корневую папку приложения Express.js, снова выберите свою учетную запись в **подписке**, а затем выберите имя созданного веб-приложения (`my-text-to-speech-app`).

1. При развертывании в Linux нажмите кнопку **Да**, когда появится запрос на обновление конфигурации, чтобы выполнить `npm install` на целевом сервере.

    ![Запрос на обновление конфигурации на целевом сервере Linux](../media/deploy-azure/server-build.png)

1. По завершении развертывания щелкните **Обзор веб-сайта** в диалоговом окне с предложением просмотреть только что развернутое веб-приложение. 

1. (Необязательно.) Вы можете внести изменения в файлы кода, а затем снова нажать кнопку **Развернуть в веб-приложении** в расширении Службы приложений Azure, чтобы обновить веб-приложение.

## <a name="stream-remote-service-logs-in-visual-studio-code"></a>Потоковая передача журналов удаленной службы в Visual Studio Code

Просматривайте любые выходные данные или выводите последний фрагмент данных, которые выполняющееся приложение создает в результате вызовов к `console.log`. Эти выходные данные отображаются в окне **Вывод** в Visual Studio Code.

1. В обозревателе **Службы приложений Azure** щелкните узел нового приложения правой кнопкой мыши и выберите **Начать потоковую передачу журналов**.

    <pre>
    Starting Live Log Stream ---
    </pre>

1. Несколько раз обновите веб-страницу в браузере и убедитесь, что отображаются дополнительные выходные данные журнала.


## <a name="clean-up-resources-by-removing-resource-group"></a>Удаление ресурсов путем удаления группы ресурсов

Завершив работу с этим руководством, удалите группу ресурсов с ресурсом, чтобы с вас не взималась плата за потребление. 

Чтобы удалить группу ресурсов, в Azure Cloud Shell выполните команду [Azure CLI](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name tutorial-resource-group-eastus  -y
```

Эта команда может выполняться несколько минут. 

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание приложения MongoDB на платформе Express.js в Службе приложений с помощью Visual Studio Code](deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)