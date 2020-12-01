---
title: Создание ресурса Компьютерного зрения
description: Создайте ресурс Компьютерного зрения Cognitive Services и сохраните его в переменные среды.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4ac324171f47ab8795169c5dd453d1e6451e8906
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993506"
---
# <a name="3-create-computer-vision-resource-and-use-in-code"></a>3. Создание ресурса Компьютерного зрения и его применение в коде

На этом этапе вы создадите ресурс Компьютерного зрения Cognitive Services и сохраните его в переменных среды. 

## <a name="create-azure-resources"></a>Создание ресурсов Azure

Создание группы ресурсов позволяет вам с легкостью находить ресурсы и удалять их, когда они больше не нужны.

Когда вы выполните эту серию действий, у вас будет **ключ и конечная точка** для ресурса.

1. В терминале или оболочке Bash введите [команду Azure CLI для создания группы ресурсов Azure](/cli/azure/group?view=azure-cli-latest#az_group_create) с именем `rg-demo`:

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo 
    ```
1. Выполните следующую команду, чтобы [создать ресурс Компьютерного зрения](/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):


    ```azurecli
    az cognitiveservices account create \
        --name demo-ComputerVision \
        --resource-group rg-demo \
        --kind ComputerVision \
        --sku F0 \
        --location eastus \
        --yes
    ```

1. В результатах найдите и скопируйте ключ `properties.endpoint`. Позже он вам понадобится.

    ```json
    ...
    "properties":{
        ...
        "endpoint": "https://eastus.api.cognitive.microsoft.com/",
        ...
    }
    ...
    ```

1. Выполните приведенную ниже [команду](/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list), чтобы получить ключи. 

    ```azurecli
    az cognitiveservices account keys list \
    --name demo-ComputerVision \
    --resource-group rg-demo
    ```

1. Скопируйте один из ключей, который пригодится чуть позже.

    ```json
    {
      "key1": "8eb7f878bdce4e96b26c89b2b8d05319",
      "key2": "c2067cea18254bdda71c8ba6428c1e1a"
    }
    ```

## <a name="add-environment-variables-to-your-local-environment"></a>Добавление переменных среды в локальную среду

Чтобы использовать ресурс, в локальном коде нужно знать ключ и адрес конечной точки. В нашей базе кода они хранятся в переменных среды:

* REACT_APP_COMPUTERVISIONKEY;
* REACT_APP_COMPUTERVISIONENDPOINT. 

1. Выполните приведенную ниже команду, чтобы добавить эти переменные в среду.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    export REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    
    # <a name="cmd"></a>[cmd](#tab/cmd)
    
    ```cmd
    set REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    set REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    ---

## <a name="add-environment-variables-to-your-remote-environment"></a>Добавление переменных среды в удаленную среду

При работе со статическими веб-приложениями Azure необходимо передавать из действия GitHub в статическое веб-приложение переменные среды, например со значениями секретов. Действие GitHub обеспечивает сборку приложения, включая в нее ключ и конечную точку Компьютерного зрения, полученные в виде секретов GitHub для репозитория. Затем код вместе с переменными среды отправляется в статическое веб-приложение.

1. В веб-браузере откройте репозиторий GitHub и последовательно выберите элементы **Параметры**, **Секреты** и **Новый секрет репозитория**.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-create-new-repository-secret.png" alt-text="Снимок части экрана: браузер с примером приложения React для анализа изображений с использованием Компьютерного зрения Cognitive Services до настройки ключа и конечной точки.":::

1. Введите имя и значение конечной точки, которые уже использовалась в предыдущем разделе. Затем создайте другой секрет с тем же именем и значением ключа, которые использовались в предыдущем разделе. 
    
    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-add-secret.png" alt-text="Введите прежние имя и значение для конечной точки. Затем создайте другой секрет с прежними именем и значением ключа.":::

## <a name="run-react-app-with-computervision-resource"></a>Запуск приложения React с ресурсом Компьютерного зрения

Наше приложение React отслеживает изменения, а также выполняет повторную сборку и запуск приложения. Внесите любые изменения в код, чтобы активировать процесс повторной сборки.

1. Например, **добавьте пустую строку** в `./src/VisualAi.js` сразу после первой пустой строки (строка 4). Будет выполнена повторная сборка локально запущенного веб-сайта.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-start-up.png" alt-text="Снимок части экрана: браузер с примером приложения React для Компьютерного зрения Cognitive Services во время ожидания ввода URL-адреса и нажатия клавиши ENTER.":::

1. Оставьте текстовое поле пустым и **нажмите кнопку Analyze (Анализ)** . 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="Снимок части экрана: браузер с примером приложения React для Компьютерного зрения Cognitive Services при возврате результата анализа.":::

    Изображение выбирается случайным образом из каталога изображений, который определен в `./src/DefaultImages.js`. 

1. Повторно нажимайте кнопку **Analyze** (Анализ), чтобы увидеть другие изображения и результаты для них. 

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Создание статического веб-приложения Azure](create-static-web-app-visual-studio-code-extension.md)