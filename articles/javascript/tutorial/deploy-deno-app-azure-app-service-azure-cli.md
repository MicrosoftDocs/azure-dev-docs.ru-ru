---
title: Развертывание приложений Deno в Службе приложений Azure с помощью Azure CLI
description: В этом руководстве объясняется, как развернуть приложение Deno в Службе приложений Azure (в Linux или Windows) с помощью Azure CLI.
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: ba2e0a42b6d2dedd2192629562a8415a0d6d7167
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772627"
---
# <a name="deploy-deno-apps-to-azure-app-service-from-the-azure-cli"></a>Развертывание приложений Deno в Службе приложений Azure с помощью Azure CLI

Разверните приложение Deno в Службе приложений Azure (в Linux или Windows) с помощью Azure CLI. Среда выполнения Deno для Службы приложений Azure предоставляется с экспериментальным образом Docker. 

![Выполнение демо-сервера](../media/deploy-azure/deno-hello-world.png)

## <a name="1-prepare-your-environment"></a>1. Подготовка среды

- Учетная запись Azure с активной подпиской. [Создать бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- Установка [Visual Studio Code](https://code.visualstudio.com/)
- Установка [Deno](https://deno.land/#installation).
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) можно использовать в среде Bash.

   [![Внедрение запуска](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)   
- При необходимости [установите](/cli/azure/install-azure-cli) Azure CLI, чтобы выполнять справочные команды CLI.
   - Если вы используете локальную установку, выполните вход с помощью команды Azure CLI [az login](/cli/azure/reference-index#az-login).  Чтобы выполнить аутентификацию, следуйте инструкциям в окне терминала.  Сведения о дополнительных возможностях, доступных при входе, см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).
  - Если появится запрос, установите расширения Azure CLI при первом использовании.  Дополнительные сведения о расширениях см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Выполните команду [az version](/cli/azure/reference-index?#az_version), чтобы узнать установленную версию и зависимые библиотеки. Чтобы обновиться до последней версии, выполните команду [az upgrade](/cli/azure/reference-index?#az_upgrade).

## <a name="2-sign-in-to-azure-cli"></a>2. Вход в Azure CLI

Если вы используете Azure CLI из локальной командной строки, перед использованием команд CLI необходимо выполнить вход с помощью [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](../../azure-cli/includes/interactive-login.md)]

3. После входа в систему появится список подписок, связанных с учетной записью Azure. В сведениях о подписке с `isDefault: true` указана текущая активная подписка, отображаемая после входа. 

4. Чтобы выбрать другую подписку, укажите идентификатор подписки с помощью команды [az account set](/cli/azure/account#az-account-set). См. дополнительные сведения о [выборе нужной подписки при использовании нескольких подписок Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

## <a name="3-create-local-deno-api-app"></a>3. Создание локального приложения API Deno

Создайте приложение Deno с помощью встроенного веб-сервера Deno. Затем вы запустите приложение локально.

1. В окне терминала или командной строки перейдите в расположение, в котором нужно создать папку приложения, и создайте новую папку с именем `deno-demo`.

1. Создайте файл с именем `demo.ts`.
1. Deno принимает выполняющийся код непосредственно из URL-адресов. Создайте HTTP-сервер, который на все запросы отвечает строкой Hello World. Используйте следующий код:

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. Запустите приложение, выполнив следующий скрипт:

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. Протестируйте приложение, перейдя по ссылке `http://localhost:80` в браузере. Сайт должен выглядеть следующим образом:

    ![Выполнение демо-сервера](../media/deploy-azure/deno-hello-world.png)

    Этот код также можно выполнить с помощью команды `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`.

1. Нажмите клавиши **CTRL**+**C** в терминале, чтобы остановить работу сервера.

## <a name="4-deploy-deno-app-to-azure"></a>4. Развертывание приложения Deno в Azure

Разверните приложение Deno в Azure с помощью Azure CLI.

1. Чтобы создать группу ресурсов с именем `deno-quickstart`, выполните следующую команду:

    ```azurecli
    az group create --name deno-quickstart --location eastus
    ```

    Если вы решите изменить имя группы ресурсов, обязательно обновите все флаги `-g` в следующих шагах.

1. Создайте план Службы приложений с именем `deno-plan`, который будет содержать ваш веб-сайт, с помощью следующей команды:

    ```azurecli
    az appservice plan create --resource-group deno-quickstart --name deno-plan --is-linux
    ```

1. Далее вы создадите само веб-приложение. Эта команда создаст экземпляр Службы приложений и свяжет ее с ранее созданным планом. Для тега `<your-app-name>` укажите значение имени, которое вы хотите присвоить веб-приложению. Оно должно быть уникальным.

    ```azurecli
    az webapp create -n <your-app-name> --resource-group deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    В этом экземпляре Службы приложений выполняется образ Docker `anthonychu/azure-webapps-deno:1.0.2`, который предоставляет базовую функциональность для выполнения любого кода Deno. Для завершения этого процесса может понадобиться несколько секунд.

## <a name="5-configure-the-azure-app-service-webapp"></a>5. Настройка веб-приложения Службы приложений Azure

1. Сообщите веб-приложению, где получить образ контейнера Docker для экспериментального имени образа Deno:

    ```azurecli
    az webapp config container set --name <your-app-name> --resource-group deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true
    ```

1. Настройте веб-приложение таким образом, чтобы файл запуска отсутствовал:

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file ''

1. Set the webapp to have runtime environment variables:

    ```azurecli
    az webapp config appsettings set --name <your-app-name> --resource-group deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

## <a name="6-configure-deno-app-deployment-to-web-app"></a>6. Настройка развертывания приложения Deno в веб-приложении 

Веб-приложение Azure настроено и готово к использованию, но еще не имеет пакета Deno. Упакуйте приложение в пакет `.zip` и сообщите веб-приложению, что файл находится на локальном компьютере. Затем установите файл запуска, который находится в ZIP-файле. 

1. Перейдите к папке `deno-demo`.

    ```bash
    cd deno-demo
    ```

1. Выполните команду `zip`.

    ```bash
    zip demo demo.ts
    ```

    Эта команда создаст файл с именем `demo.zip` в той же папке, где находится файл `demo.ts`.

1. Настройте пакет в качестве источника кода для развертывания:

    ```azurecli
    az webapp deployment source config-zip --name <your-app-name> --resource-group deno-quickstart --src ./demo.zip
    ```

1. Настройте для запуска файл в пакете:

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. Протестируйте приложение, перейдя по адресу `https://<your-app-name>.azurewebsites.net`. 

## <a name="7-clean-up-resources"></a>7. Очистка ресурсов

Удалите группу ресурсов, которая также удаляет ресурсы веб-приложения, с помощью следующей команды:

```azurecli
az group delete deno-quickstart
```

## <a name="next-steps"></a>Дальнейшие действия

См. также:
* [Развертывание в Службе приложений](../tutorial-vscode-azure-app-service-node-01.md) с помощью расширений Visual Studio Code
* [Развертывание в виртуальной машине](./nodejs-virtual-machine-vm/introduction.md)
* [Развертывание функции Deno](https://github.com/anthonychu/azure-functions-deno-worker) в качестве [настраиваемого обработчика](/azure/azure-functions/functions-custom-handlers)