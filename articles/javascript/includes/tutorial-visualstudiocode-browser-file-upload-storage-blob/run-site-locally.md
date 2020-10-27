---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: include file run-site-locally.md
description: include file run-site-locally.md
ms.openlocfilehash: 129546c7f6a845c335405c6fc615f907848138f6
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344210"
---
В этом разделе руководства показано, как скачать пример приложения на локальный компьютер и запустить его в терминале Visual Studio Code. Затем вы откроете локально запущенное приложение в браузере.

## <a name="clone-and-run-the-initial-react-app"></a>Клонирование и запуск начальной версии приложения React

Мы предоставляем вам полноценное приложение React. В этой процедуре вы клонируете приложение, установите зависимости и запустите приложение. Веб-приложение начальной версии попытается подключиться к службе хранилища Azure, если оно настроено в коде, или вернет сообщение `Storage is not configured`, если хранилище недоступно. 

1. Откройте Visual Studio Code.
1. Клонируйте репозиторий GitHub, щелкнув значок Git и выбрав действие **Клонировать репозиторий** . Чтобы выполнить это, вам нужно войти в GitHub. Укажите URL-адрес репозитория `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`, а затем выберите на локальном компьютере папку, в которую будет скопирован пример. При появлении запроса откройте клонированный репозиторий. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Клонируйте репозиторий GitHub, щелкнув значок Git и выбрав действие &quot;Клонировать репозиторий&quot;.":::

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

    :::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="Клонируйте репозиторий GitHub, щелкнув значок Git и выбрав действие &quot;Клонировать репозиторий&quot;.":::

1. Остановите выполнение кода, нажав клавиши Control+C в терминале Visual Studio Code.
