---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: include file understand-the-code.md
description: include file understand-the-code.md
ms.openlocfilehash: fd9d2eb2c6c8dd3a39d737cdcdf609b9cf04cf7e
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344242"
---
В этом разделе руководства вы ознакомитесь с примером кода, который позволяет отправить файл из веб-приложения.

## <a name="sample-created-with-create-react-app"></a>Пример, созданный с помощью create-react-app

Этот [пример](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob) представляет собой простое приложение React, созданное с помощью [create-react-app](https://create-react-app.dev/docs/adding-typescript/) и шаблона TypeScript.

```typescript
npx create-react-app my-app --template typescript
```

Платформа create-react-app предоставляет следующие преимущества:
* автоматическое объединение файлов, которое является обязательным условием для использования клиентских библиотек Azure в браузере;
* предоставление скриптов сборки и транспиляции кода. 

## <a name="upload-button-functionality"></a>Функция кнопки отправки

Файл `src/app` создается автоматически при создании приложения с помощью create-react-app. Мы изменили этот файл, включив в него описание кнопки выбора файла и кнопки отправки, а также вспомогательный код для поддержки этой возможности. 

Здесь выделен код, который подключается к коду хранилища BLOB-объектов Azure. Вызов `uploadFileToBlob` возвращает в виде неструктурированного списка все BLOB-объекты (файлы) в указанном контейнере. Этот список отображается с помощью функции `DisplayImagesFromContainer`.

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/App.tsx" highlight="3,28":::

## <a name="upload-file-to-azure-storage-blob-with-azure-sdk-client-library"></a>Отправка файла в хранилище BLOB-объектов Azure с помощью клиентской библиотеки пакета SDK для Azure

Код для отправки файла в службу хранилища Azure не зависит от платформы. Поскольку это учебный код, его структура оптимизирована для простоты и удобства понимания. Выбранные решения описаны ниже, и вы можете использовать их в качестве основы для оценки целей использования, безопасности и эффективности собственного проекта. 

В этом примере создаются и применяются общедоступные контейнер и файлы. Защитить файлы в проекте можно на разных уровнях — от обязательной проверки подлинности при доступе к ресурсу до точной настройки разрешений для каждого BLOB-объекта. 

### <a name="dependencies-and-variables"></a>Зависимости и переменные

Файл `uploadToBlob.ts` загружает зависимости и извлекает необходимые переменные из переменных среды или жестко запрограммированных строк.

| Переменная | Описание |
|--|--|
|`sasToken`|Маркер SAS, созданный на портале Azure, имеет префикс `?`. Удалите префикс, прежде чем добавить маркер в переменную `sasToken`.| 
|`container`|Имя контейнера в хранилище BLOB-объектов. Этот элемент является эквивалентом папки или каталога в обычной файловой системе.|
|`storageAccountName`|Имя ресурса.|

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="2,5,16" id="snippet_package":::

### <a name="security-for-azure-credentials"></a>Безопасность учетных данных Azure

Оцените, где вы предпочитаете хранить секреты для своего проекта, например маркер SAS. Если вашему приложению требуется защита данных в Azure, разместите приведенный здесь код хранения не на клиенте, а в [функции Azure](/azure/azure-functions/), откуда вы будете вызывать его в приложении React.  

### <a name="create-storage-client-and-manage-steps"></a>Инструкции по созданию клиента хранилища и управлению им

Функция `uploadFileToBlob` является основной функцией в этом файле. Она создает объект клиента для службы хранилища, затем создает клиент для объекта контейнера, отправляет файл и получает список всех BLOB-объектов, сохраненных в контейнере. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="5,6,7" id="snippet_uploadFileToBlob":::

### <a name="upload-file-to-blob"></a>Отправка файла в BLOB-объект

Функция `createBlobInContainer` передает файл в контейнер с помощью метода `uploadBrowserData` из клиентской библиотеки. Вместе с запросом нужно передавать тип содержимого, если вы планируете использовать функциональные возможности браузера, которые зависят от типа файла, например для отображения изображений. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_createBlobInContainer":::

### <a name="get-list-of-blobs"></a>Получение списка BLOB-объектов

Функция `getBlobsInContainer` возвращает список URL-адресов всех BLOB-объектов, сохраненных в контейнере. Эти URL-адреса будут включены в параметр `src` тега изображения в HTML-коде следующим образом: `<img src={item} alt={item} height="200" />`. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_getBlobsInContainer":::

