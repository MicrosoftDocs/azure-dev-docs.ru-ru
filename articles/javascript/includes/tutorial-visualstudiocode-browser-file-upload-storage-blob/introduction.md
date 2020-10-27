---
title: include file tutorial-azure-web-app-mongodb-00.md
description: include file tutorial-azure-web-app-mongodb-00.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: b9ab409c845b7690474eb47117df4401ecaf59cd
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344234"
---
В этом руководстве для передачи файла в BLOB-объект службы хранилища Azure используется приложение React. 

Вся работа по программированию уже сделана заранее, и это руководство полностью посвящено эффективному использованию локальных и удаленных сред Azure в Visual Studio Code с расширениями Azure.

## <a name="top-tasks"></a>Основные задачи

В этом руководстве представлено несколько **основных задач Azure** для разработчиков JavaScript:

* запуск приложения React на локальном компьютере с помощью Visual Studio Code;
* создание ресурса хранилища и его настройка для отправки файлов;
    * Настройка CORS
    * создание маркера подписанного URL-адреса (SAS);
* настройка кода для клиентской библиотеки Azure SDK для проверки подлинности в службе с помощью маркера SAS.

## <a name="sample-application"></a>Пример приложения

Наш [пример приложения React, который размещен на сайте GitHub](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob), состоит из следующих элементов:

* **приложение React** , размещенное на порту 3000;
* скрипт клиентской библиотеки Azure SDK для отправки в большие двоичные объекты хранилища.

:::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Простое приложение React, подключенное к BLOB-объектам службы хранилища Azure.":::
