---
title: Отправка образа в Хранилище BLOB-объектов с помощью VSCode — Служба приложений/CosmosDB
description: Используйте приложение React или TypeScript для передачи файла в BLOB-объекты службы хранилища Azure. В этом руководстве рассматривается использование локальных и удаленных сред с расширениями Visual Studio Code.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript, azure-sdk-storage-blob-typescript-version-12.2.1
ms.openlocfilehash: 1f66ba54aa6cfccdda96a51bb610350a10fb7eef
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97689523"
---
# <a name="upload-an-image-to-an-azure-storage-blob"></a>Отправка образа в Azure Storage Blob

Используйте приложение React на стороне клиента, чтобы передать файл образа в Azure Storage Blob с помощью пакета npm службы хранилища Azure. 

Вся работа по программированию на TypeScript уже сделана заранее, и это руководство полностью посвящено эффективному использованию локальных и удаленных сред Azure в Visual Studio Code с расширениями Azure.

* [**Образец кода**](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)

## <a name="application-architecture-and-functionality"></a>Архитектура и функциональные возможности приложения

В этом руководстве представлено несколько основных задач Azure для разработчиков JavaScript:

* запуск приложения React или TypeScript на локальном компьютере с помощью Visual Studio Code;
* создание ресурса **Azure Storage Blob** и его настройка для отправки файлов;
    * Настройка CORS
    * создание маркера подписанного URL-адреса (SAS);
* настройка кода для клиентской библиотеки Azure SDK для проверки подлинности в службе с помощью маркера SAS.

Наш [пример приложения React, который размещен на сайте GitHub](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob), состоит из следующих элементов:

* **приложение React**, размещенное на порту 3000;
* **скрипт клиентской библиотеки Azure SDK** для отправки данных в большие двоичные объекты хранилища.

:::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Простое приложение React, подключенное к BLOB-объектам службы хранилища Azure.":::

## <a name="1-set-up-development-environment"></a>1. Настройка среды разработки

- Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/).
- [Node.js 10 и npm ](https://nodejs.org/en/download), диспетчер пакетов Node.js, установленный на локальном компьютере.
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
- Расширения Visual Studio Code:
    - [Служба хранилища Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) — используется для просмотра ресурса хранилища.

## <a name="2-clone-and-run-the-initial-react-app"></a>2. Клонирование и запуск начальной версии приложения React

1. Откройте Visual Studio Code.
1. Клонируйте репозиторий GitHub, щелкнув значок Git и выбрав действие **Клонировать репозиторий**. Для этого вам нужно войти в GitHub. Укажите URL-адрес репозитория: 

    `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`

1. Выберите на локальном компьютере папку, в которую будет скопирован пример. При появлении запроса откройте клонированный репозиторий. 

    :::image type="content" source="../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Клонируйте репозиторий GitHub, щелкнув значок Git и выбрав действие &quot;Клонировать репозиторий&quot;.":::

1. В Visual Studio Code откройте окно терминала и выполните следующую команду, чтобы установить зависимости этого примера.

    ```javascript
    npm install
    ```

1. В том же окне терминала выполните команду для запуска веб-приложения.

    ```javascript
    npm start
    ```

1. Чтобы просмотреть веб-приложение на локальном компьютере, откройте страницу с приведенным ниже URL-адресом в любом веб-браузере.

    ```url
    http://localhost:3000/
    ```

    Если в браузере отобразится простое веб-приложение с текстовым сообщением о том, что хранилище не настроено, значит вы успешно выполнили инструкции из этого раздела руководства.

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="Простое приложение Node.js, подключенное к базе данных MongoDB.":::

1. Остановите выполнение кода, нажав клавиши Control+C в терминале Visual Studio Code.

## <a name="3-create-storage-resource-with-visual-studio-extension"></a>3. Создание ресурса службы хранилища с помощью расширения Visual Studio

1. Перейдите к расширению службы хранилища Azure. Щелкните подписку правой кнопкой мыши и выберите действие `Create Storage Account...`.

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource.png" alt-text="Перейдите к расширению службы хранилища Azure. Щелкните правой кнопкой мыши подписку и выберите команду &quot;Создание учетной записи хранения...&quot;.":::

1. Следуйте инструкциям в следующей таблице, чтобы понять, как создать ресурс хранилища.

    |Свойство|Значение|
    |--|--|
    |Введите глобально уникальное имя для нового веб-приложения.| Введите значение для ресурса службы хранилища, например `fileuploaddemo`.<br><br> Это уникальное имя является **именем ресурса**, который используется в следующем разделе. Оно может включать только символы и цифры, а его длина не должна превышать 24 символа. Это **имя учетной записи** потребуется в дальнейшем.|

1. Когда завершится процесс создания приложения, появится уведомление со сведениями о новом ресурсе. 

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource-complete.png" alt-text="Когда завершится процесс создания приложения, появится уведомление со сведениями о новом ресурсе.":::

## <a name="4-set-storage-account-name-in-code-file"></a>4. Задание имени учетной записи хранения в файле кода

Задайте имя ресурса из предыдущего шага в `src/azure-storage-blob.ts`. 

```typescript
const storageAccountName = process.env.storageresourcename || "fileuploaddemo"; 
```

## <a name="5-generate-your-shared-access-signature-sas-token"></a>5. Создание маркера подписанного URL-адреса (SAS) 

Прежде чем настраивать CORS, нужно создать маркер SAS. 

1. В расширении службы хранилища для Visual Studio Code щелкните ресурс правой кнопкой мыши и выберите **Открыть на портале**. Откроется страница этого ресурса службы хранилища на портале Azure.
1. В разделе **Параметры** выберите элемент **Подписанный URL-адрес**. 
1. Настройте для маркера SAS следующие параметры. 

    | Свойство|Значение|
    |--|--|
    |Разрешенные службы|BLOB-объект|
    |Допустимые типы ресурсов|Служба, контейнер, объект|
    |Допустимые разрешения|Чтение, запись, удаление, перечисление, добавление, создание|
    |Возможность удаления версий|Флажок установлен|
    |Дата и время начала и окончания срока действия|Подтвердите дату и время начала действия, а дату и время окончания **установите через сутки**. Этот маркер SAS будет действителен только в течение 24 часов.|
    |Только HTTPS|Выбрано|
    |Предпочтительный уровень маршрутизации|Basic|
    |Ключ подписывания|Выбран key1|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-generate-sas-token.png" alt-text="Настройте маркер SAS, как показано на изображении. Эти параметры объясняются под изображением.":::

1. Выберите действие **Создать SAS и строку подключения**. Сразу же скопируйте маркер SAS. Вы не сможете получить этот маркер позднее. Если не скопировать его сейчас, придется создавать новый маркер SAS. 

## <a name="set-sas-token-in-code-file"></a>Настройка маркера SAS в файле кода

Маркер SAS используется при выполнении запросов к облачному ресурсу.

1. Если маркер начинается с вопросительного знака, удалите `?`. Файл кода подставляет `?` автоматически, поэтому его не нужно включать в маркер.

1. В файле `src/azure-storage-blob.ts` присвойте маркер SAS в качестве значения параметра sasToken, добавив маркер SAS в пустую строку. Сохраните остальную часть кода неизменной. 

```typescript
// remove `?` if it is first character of token
const sasToken = process.env.storagesastoken || "";
```

## <a name="6-configure-cors-for-azure-storage-resource"></a>6. Настройка CORS для ресурса службы хранилища Azure

Настройте CORS для созданного ресурса, чтобы код React на стороне клиента мог получить доступ к этой учетной записи хранения. 

1. В том же окне портала Azure откройте раздел "Параметры" и выберите **CORS**. 
1. Настройте CORS, как показано на изображении. Эти параметры объясняются под изображением. 

    | Свойство|Значение|
    |--|--|
    |Разрешенные источники|`*`|
    |Допустимые методы|Все, кроме исправлений.|
    |Допустимые заголовки|`*`|
    |Предоставляемые заголовки|`*`|
    |Максимальный возраст|86400|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-cors.png" alt-text="Настройте CORS, как показано на изображении. Эти параметры объясняются под изображением.":::

1. Щелкните **Сохранить** над разделом с параметрами, чтобы сохранить их для ресурса. Для работы с этими параметрами CORS никакие изменения в коде не требуются. 

## <a name="7-run-project-locally-to-verify-connection-to-storage-account"></a>7. Запуск проекта в локальной среде для проверки подключения к учетной записи хранения

Итак, маркер SAS и имя учетной записи хранения настроены в файле `src/azure-storage-blob.ts`, и теперь все готово к запуску приложения.

1. Если приложение не работает, запустите его еще раз:

    ```javascript
    npm start
    ```

1. Откройте следующий URL-адрес в браузере:

    `http://localhost:3000` 

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-configured-upload-button-displayed.png" alt-text="Веб-сайт React, подключенный к хранилищу больших двоичных объектов Azure, с кнопками для выбора и отправки файла.":::

1. Выберите для отправки изображение из папки `images` и нажмите кнопку **Отправить!** . . 

    Клиентский код интерфейса React вызывает `src/azure-storage-blob.ts` для проверки подлинности в Azure, а затем создает контейнер службы хранилища (если он не существует) и отправляет в него файл. 

    Вы выполнили инструкции из этого руководства. В остальных разделах описывается приложение и приводятся советы по устранению проблем.

## <a name="troubleshoot-local-connection-to-storage-account"></a>Устранение неполадок локального подключения к учетной записи хранения

Если вы получили сообщение об ошибке или вам не удалось отправить файл в контейнер, проверьте следующее:

* Повторно создайте маркер SAS. При этом убедитесь, что маркер создан на уровне ресурса хранилища, а не на уровне контейнера. Скопируйте новый маркер в код в правильное расположение.
* Убедитесь, что строка маркера, скопированная в код, не содержит символ `?` (вопросительный знак) в начале.
* Проверьте параметр CORS для ресурса хранилища.

## <a name="upload-button-functionality"></a>Функция кнопки отправки

Файл TypeScript `src/App.tsx` создается автоматически при создании приложения с помощью create-react-app. Мы изменили этот файл, включив в него описание кнопки выбора файла и кнопки отправки, а также вспомогательный код для поддержки этой возможности. 

Здесь выделен код, который подключается к коду хранилища BLOB-объектов Azure. Вызов `uploadFileToBlob` возвращает в виде неструктурированного списка все BLOB-объекты (файлы) в указанном контейнере. Этот список отображается с помощью функции `DisplayImagesFromContainer`.

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/App.tsx" highlight="3,28":::

## <a name="upload-file-to-azure-storage-blob-with-azure-sdk-client-library"></a>Отправка файла в хранилище BLOB-объектов Azure с помощью клиентской библиотеки пакета SDK для Azure

Код для отправки файла в службу хранилища Azure не зависит от платформы. Поскольку это учебный код, его структура оптимизирована для простоты и удобства понимания. Выбранные решения описаны ниже, и вы можете использовать их в качестве основы для оценки целей использования, безопасности и эффективности собственного проекта. 

В этом примере создаются и применяются общедоступные контейнер и файлы. Защитить файлы в проекте можно на разных уровнях — от обязательной проверки подлинности при доступе к ресурсу до точной настройки разрешений для каждого BLOB-объекта. 

### <a name="dependencies-and-variables"></a>Зависимости и переменные

Файл TypeScript `azure-storage-blob.ts` загружает зависимости и извлекает необходимые переменные из переменных среды или жестко заданных строк.

| Переменная | Описание |
|--|--|
|`sasToken`|Маркер SAS, созданный на портале Azure, имеет префикс `?`. Удалите префикс, прежде чем добавить маркер в переменную `sasToken`.| 
|`container`|Имя контейнера в хранилище BLOB-объектов. Этот элемент является эквивалентом папки или каталога в обычной файловой системе.|
|`storageAccountName`|Имя ресурса.|

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="2,5,16" id="snippet_package":::

### <a name="security-for-azure-credentials"></a>Безопасность учетных данных Azure

Оцените, где вы предпочитаете хранить секреты для своего проекта, например маркер SAS. Если вашему приложению требуется защита данных в Azure, разместите приведенный здесь код хранения не на клиенте, а в [функции Azure](/azure/azure-functions/), откуда вы будете вызывать его в приложении React.  

### <a name="create-storage-client-and-manage-steps"></a>Инструкции по созданию клиента хранилища и управлению им

Функция `uploadFileToBlob` является основной функцией в этом файле. Она создает объект клиента для службы хранилища, затем создает клиент для объекта контейнера, отправляет файл и получает список всех BLOB-объектов, сохраненных в контейнере. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="5,6,7" id="snippet_uploadFileToBlob":::

### <a name="upload-file-to-blob"></a>Отправка файла в BLOB-объект

Функция `createBlobInContainer` передает файл в контейнер с помощью метода `uploadBrowserData` из клиентской библиотеки. Вместе с запросом нужно передавать тип содержимого, если вы планируете использовать функциональные возможности браузера, которые зависят от типа файла, например для отображения изображений. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="10" id="snippet_createBlobInContainer":::

### <a name="get-list-of-blobs"></a>Получение списка BLOB-объектов

Функция `getBlobsInContainer` возвращает список URL-адресов всех BLOB-объектов, сохраненных в контейнере. Эти URL-адреса будут включены в параметр `src` тега изображения в HTML-коде следующим образом: `<img src={item} alt={item} height="200" />`. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="10" id="snippet_getBlobsInContainer":::

## <a name="clean-up-resources"></a>Очистка ресурсов

В Visual Studio Code откройте Обозреватель службы хранилища Azure, щелкните ресурс правой кнопкой мыши и выберите **Удаление учетной записи хранения…**

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите продолжить работу с этим приложением, узнайте, как разместить приложение в Azure, развернув его с помощью одного из следующих вариантов:

* [отправка статического веб-приложения;](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
* отправка в ресурс веб-приложения с помощью [расширения Visual Studio Code для Службы приложений](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice);
* [отправка приложения на виртуальную машину Azure.](nodejs-virtual-machine-vm/introduction.md)