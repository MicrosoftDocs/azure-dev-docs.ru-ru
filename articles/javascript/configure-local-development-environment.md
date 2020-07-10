---
title: Настройка локальной среды JavaScript для разработки в Azure
description: Сведения о настройке локальной среды разработки JavaScript для работы с Azure, включая редактор, библиотеки Azure SDK, дополнительные средства и необходимые учетные данные для аутентификации библиотек.
ms.date: 07/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2285e79ea62d2de961fd7d4dc7647fec2312b83c
ms.sourcegitcommit: 7be67fb768fb5e19f7de573068cc1376b3d90d1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85911193"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Настройка локальной среды разработки JavaScript для Azure

При создании облачных приложений разработчики обычно предпочитают тестировать код на локальных рабочих станциях до его развертывания в облачной среде, такой как Azure. Локальная разработка позволяет использовать более широкий ряд инструментов и знакомую среду разработки.

В этой статье приведены инструкции по настройке для создания и проверки локальной среды разработки, которая подходит для использования JavaScript в Azure.

## <a name="one-time-subscription-creation"></a>Разовый процесс создания подписки

Ресурсы Azure создаются в подписке, которая является единицей выставления счетов при использовании Azure. Хотя вы можете создавать бесплатные ресурсы (каждая подписка предлагает бесплатный ресурс для большинства служб), вам нужно будет создать ресурсы платного уровня, если вы планируете развертывать ресурсы в рабочей среде.

* Если у вас уже есть подписка, новую создавать не нужно. Войдите на [портал Azure](https://portal.azure.com), чтобы получить доступ к существующей подписке.
* [Подписка на бесплатную пробную версию]()

## <a name="one-time-installation"></a>Однократная установка

Для разработки с использованием ресурсов Azure с JavaScript на локальной рабочей станции вам требуется однократно установить следующие компоненты:

|Имя или установщик|Описание|
|--|--|
|[Node.js]()|Установите последнюю версию среды выполнения с долгосрочной поддержкой (LTS) для разработки на локальной рабочей станции. |
| NPM (устанавливается с последними версиями Node.js) или [Yarn]().|Диспетчер пакетов для установки библиотек Azure SDK.|
|[Introductory Videos](https://aka.ms/vscode-deploy) (Вводные видео)| VS Code обеспечивает интеграцию с JavaScript и предоставляет все необходимое для написания кода, но устанавливать именно это решение необязательно. Вы можете использовать любой удобный редактор кода. Если вы используете другой редактор, для работы с этим руководством проверьте наличие интеграции с Azure или используйте Azure CLI.|
|[Azure CLI]()|Azure CLI можно использовать для создания ресурсов Azure и управления ими из командной строки, терминала или оболочки Bash.|

> [!CAUTION]
> Если вы планируете использовать ресурс Azure в качестве среды выполнения для своего кода, например веб-приложения Azure или экземпляра контейнера Azure, убедитесь, что ваша локальная среда разработки Node.js соответствует среде выполнения ресурсов Azure, которую вы планируете использовать.

### <a name="optional-local-installations"></a>Дополнительные локальные установки

Следующие необязательные общие установки рабочей станции можно использовать для выполнения задач локальной разработки.

|Имя или установщик|Описание|
|--|--|
| [git](https://git-scm.com/downloads) | Средства командной строки для системы управления версиями. При желании вы можете использовать другие средства системы управления версиями. |

## <a name="one-time-configuration-of-service-principal"></a>Однократная настройка субъекта-службы

Каждая служба Azure имеет механизм аутентификации. Это может быть реализовано с помощью ключей и конечных точек, строк подключений, а также других механизмов. Чтобы обеспечить соответствие рекомендациям, создайте ресурсы и обеспечьте выполнение аутентификации в них с помощью субъекта-службы. Субъект-служба позволяет точно определить область доступа для немедленных нужд разработки.

В целом шаги по созданию и использованию субъекта-службы включают следующее:

* Войдите в Azure с помощью учетной записи отдельного пользователя, например joe@microsoft.com.
* Создайте именованный субъект-службу с определенной областью. Так как для работы с большинством кратких руководств вам нужно создать ресурс Azure, субъект-служба должен иметь возможность создавать ресурсы.
* Выйдите из своей учетной записи Azure.
* Выполните программную аутентификацию в Azure с помощью субъекта-службы.
* Субъект-служба создаст ресурс Azure и начнет использовать службу, связанную со службой.

### <a name="create-service-principal"></a>Создание субъекта-службы

Чтобы упростить создание субъекта-службы, выполните следующие действия и используйте предоставленный скрипт для создания субъекта-службы, который вы сможете использовать при работе с краткими руководствами по Azure. В следующих шагах в качестве примера имени пользователя используется `JOE`. Замените его своим именем или псевдонимом электронной почты.

1. Откройте VS Code и установите расширение с [инструментами Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Оно позволяет построчно выполнять команды Azure CLI из файла скрипта. При выполнении каждой команды в VS Code открывается соседний документ для вывода результатов.

1. Создайте файл с именем `create-service-principal.sh` и скопируйте в него следующие команды Azure:

    ```azurecli
    # Replace ALL-CAPS variables with your own values

    ####################################
    # Login as you
    ####################################

    # Login - command opens browser, select your account to finish authentication, then close browser
    az login

    ####################################
    # Optional, set default subscription
    ####################################

    # If you have more than 1 subscription, use the `list` command to find the subscription, then use the `set` command to set the default by name or id
    az account list
    az account set --subscription MYCOMPANYSUBSCRIPTION

    ####################################
    # Create service principal
    ####################################

    # Create a service principal with a name that indicates its purpose and owner - the response includes the `appId` which is necessary in some of the remaining commands
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment

    ####################################
    # Add role of contributor
    ####################################

    # Add contributor role to service principal so it can create Azure resources
    az role assignment create --assignee APP-ID --role CONTRIBUTOR

    ####################################
    # Optional, verify role assignment
    ####################################

    # Verify role assignment for service principal
    az role assignment list --assignee APP-ID

    ####################################
    # Logout
    ####################################

    # Logout off Azure CLI
    az logout
    ```

    Для выполнения остальных шагов в этой процедуре поместите курсор VS Code на каждую строку файла, которая **не** начинается с символа `#`, **щелкните правой кнопкой мыши** и выберите **Выполнить строку в редакторе**.

    :::image type="content" source="media/development-setup/vscode-rightclick-run-line-in-editor.png" alt-text="Для выполнения остальных шагов в этой процедуре поместите курсор VS Code на каждую строку файла, которая не начинается с символа #, щелкните правой кнопкой мыши и выберите "Выполнить строку в редакторе".":::

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы выполнить аутентификацию в Azure со своей учетной записью пользователя с помощью Azure CLI. Эта команда откроет интернет-браузер. Выберите учетную запись Azure. После аутентификации учетной записи закройте окно браузера (оно не понадобится вам при выполнении оставшихся задач).

    ```azurecli
    az login
    ```

    Ответ включает все подписки, к которым у вас есть доступ, отображаемые в другом окне документа VS Code в виде массива JSON. Найдите свойство `name` или `id`. Одно из этих значений потребуется вам для выполнения оставшихся команд.

    ```json
    [  {
    "cloudName": "AzureCloud",
    "id": "320d9379-aaaa-bbbb-cccc-52f2b0fc40ac",
    "isDefault": false,
    "name": "contoso-development-team",
    "state": "Enabled",
    "tenantId": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47",
    "user": {
      "name": "joe@contoso.com",
      "type": "user"
    }
    }]
    ```

    Подписка с меткой `isDefault: true` является подпиской, для которой будут выполняться оставшиеся команды. Если вам нужно изменить подписку по умолчанию, воспользуйтесь командой `az account set --subscription <name or id>`.


<a name='create-service-principal-command'></a>

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы создать субъект-службу, связанный с вашей учетной записью пользователя. У этого субъекта-службы пока нет области разрешений, так как использовался параметр `--skip-assignment`.


    ```azurecli
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment
    ```

    Имя субъекта-службы: `JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS`. Вы можете видеть все субъекты-службы, связанные с вашей учетной записью пользователя Azure, на портале Azure в списке приложений службы Active Directory.

    Результат включает нужные вам данные: `appId` и `password`. Сохраните файл с именем `create-service-principal.json`.

    ```json
    {
      "appId": "93453d56-aaaa-bbbb-cccc-db600ecc4f6a",
      "displayName": "JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "name": "http://JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "password": "d88b21e0-aaaa-bbbb-cccc-e1e9b06d50f6",
      "tenant": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47"
    }
    ```

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы назначить область разрешений для создания ресурсов Azure. Область `CONTRIBUTOR` позволяет субъекту-службе создавать ресурсы Azure.

    ```azurecli
    az role assignment create --assignee APP-ID --role CONTRIBUTOR
    ```

    Результат имеет следующий вид.

    ```json
    {
      "canDelegate": null,
      "id": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleAssignments/3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "name": "3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "principalId": "c05d56c9-aaaa-bbbb-cccc-0535d6167ed4",
      "principalType": "ServicePrincipal",
      "roleDefinitionId": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleDefinitions/b24988ac-aaaa-bbbb-cccc-20f7382dd24c",
      "scope": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

    На этом этапе субъект-служба готов к использованию.

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы выйти из Azure CLI с помощью следующей команды:

    ```azurecli
    az logout
    ```

## <a name="steps-for-each-new-development-project-setup"></a>Шаги для каждой новой настройки проекта разработки

Так как библиотеки Azure SDK предоставляются отдельно для каждой службы, не существует отдельного скачиваемого пакета для предоставления доступа ко всем ресурсам Azure. Вам нужно установить соответствующие библиотеки для тех служб Azure, которые вы хотите использовать.

Для каждого нового проекта, использующего Azure, необходимо сделать следующее:
- Создать ресурсы Azure или найти данные аутентификации для существующих ресурсов Azure.
- Установить библиотеки Azure SDK из NPM или Yarn. Подробные сведения о версиях библиотек можно найти [здесь](#library-versions).
- Обеспечить безопасное управление данными аутентификации в проекте. Один из распространенных способов — использовать **[Dotenv](https://www.npmjs.com/package/dotenv)** для считывания переменных среды из файла `.env`. Обязательно добавьте файл `.env` к файлу `.gitignore`, чтобы файл `.env` не синхронизировался с системой управления версиями.

### <a name="library-versions"></a>Версии библиотек

Все библиотеки Azure перемещаются в область `@azure`.

| Тип библиотеки | Описание|
|--|--|
|Современная|Предназначена для области `@azure`, например [@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob) и [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos), а также включает типы TypeScript.|
|Старые пакеты|Обычно начинаются с `azure-`. Многие пакеты начинаются с этого имени, которое не было предоставлено корпорацией Майкрософт. Убедитесь, что владельцем пакета является корпорация Майкрософт или Azure.|

### <a name="create-resource-using-service-principal"></a>Создание ресурса с помощью субъекта-службы

В следующем разделе показано, как создать ресурс службы Azure с помощью субъекта-службы. Чтобы выполнить вход с помощью субъекта-службы, вам нужно указать значения `appId`, `tenant` и `password`, сохраненные в ходе выполнения процедуры [Создание субъекта-службы](#create-service-principal), в файле `create-service-principal.json`.

1. Откройте VS Code и запустите ранее установленное расширение с [инструментами Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Оно позволяет построчно выполнять команды Azure CLI из файла скрипта. При выполнении каждой команды в VS Code открывается соседний документ для вывода результатов.

1. Создайте файл с именем `create-service-resource.sh` и скопируйте в него следующие команды Azure:

    ```azurecli
    ####################################
    # Login as service principal
    ####################################
    # User name for command is the app id
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID

    ####################################
    # Create resource group
    ####################################

    # Create resource group in westus region - check your quickstart if it requires a specific region, then change this value to the appropriate region
    # Common naming convention for resource group is `USERNAME-REGION-PURPOSE`
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP

    ####################################
    # Create specific service resource
    ####################################

    # Create resource in westus
    # This is an example of creating a Cognitive Services LUIS resource
    # Review your quickstart to find the exact command
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes

    ####################################
    # Get resource keys
    ####################################

    # Get resource keys
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы выполнить вход с помощью субъекта-службы. Переменные прописными буквами были возвращены [предыдущей командой для создания субъекта-службы](#create-service-principal-command).

    ```azurecli
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
    ```

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы создать группу ресурсов для всех ресурсов, которые вы создадите для работы с этим кратким руководством. Регион группы ресурсов может содержать только ресурсы из такого региона.

    ```azurecli
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

    По завершении работы с ресурсами из этого краткого руководства вы можете удалить группу ресурсов. При этом также будут удалены все ресурсы.

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы создать ресурс LUIS Cognitive Services. Это пример. Команда для вашего ресурса будет выглядеть иначе.

    ```azurecli
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes
    ```

    Ресурс LUIS использует ключ и конечную точку, которые потребуются вам для работы с краткими руководствами по LUIS.

1. Щелкните правой кнопкой мыши в следующей строке и выберите "Выполнить строку в редакторе", чтобы получить ключ и конечную точку LUIS. При аутентификации в LUIS используются ключ и конечная точка.

    ```azurecli
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

### <a name="create-environment-variables-for-the-azure-libraries"></a>Создание переменных среды для библиотек Azure

Чтобы использовать параметры Azure, которые требуются библиотекам Azure SDK для получения доступа к облаку Azure, задайте переменным среды распространенные значения. Следующие команды задают переменные среды на локальной рабочей станции. Еще один распространенный механизм — использовать пакет NPM `DOTENV` для создания файла `.env` для этих параметров. Если вы планируете использовать файл `.env`, убедитесь, что он не синхронизируется с системой управления версиями. Стандартным способом обеспечения синхронизации этих параметров с системой управления версиями является добавление файла `.env` в файл Git `.ignore`.

В следующих примерах идентификатор клиента является также идентификатором субъекта-службы и секретом субъекта-службы.

# <a name="bash"></a>[bash](#tab/bash)

```bash
AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
```

---

Замените приведенные в этих командах значения теми, которые относятся к конкретной субъект-службе.

### <a name="install-npm-packages"></a>Установка пакетов NPM

Для каждого проекта рекомендуется всегда создавать отдельную папку и собственный файл `package.json`, выполняя указанные ниже действия:

1. Откройте терминал, командную строку или оболочку Bash и создайте папку для проекта. Затем перейдите в эту папку.

    ```console
    mkdir MY-NEW-PROJECT && cd MY-NEW-PROJECT
    ```

1. Инициализируйте файл пакета:

    ```console
    npm init -y
    ```

    Эта команда выполняет команду NPM для создания файла package.json и инициализации минимальных свойств. При установке пакетов библиотеки Azure SDK с помощью NPM или Yarn в файле package.json будут сохранены эти данные об установке.

1. Установите библиотеки Azure SDK, которые потребуются вам для работы с кратким руководством. Ниже приведен пример команды.

    ```console
    npm install @azure/cognitiveservices-luis-runtime
    ```

## <a name="use-source-control"></a>Использование системы управления версиями

Мы рекомендуем всегда создавать репозиторий системы управления версиями при каждом запуске проекта. Если вы установили Git, выполните следующую команду:

```bash
git init
```

Здесь можно выполнять такие команды, как `git add` и `git commit`, чтобы фиксировать изменения. Регулярно фиксируя изменения, вы создаете журнал фиксаций, с помощью которого можно вернуться к любому предыдущему состоянию.

Чтобы выполнить оперативное резервное копирование проекта, мы также рекомендуем отправить репозиторий в [GitHub](https://github.com) или [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops). Если сначала вы инициализируете локальный репозиторий, используйте `git remote add`, чтобы подключить локальный репозиторий к GitHub или Azure DevOps.

Документацию по Git можно найти по адресу [git-scm.com/docs](https://git-scm.com/docs) и в Интернете.

Visual Studio Code включает ряд встроенных функций Git. Дополнительные сведения в см. в статье [Использование системы управления версиями в VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

Вы также можете использовать любую другую систему управления версиями по своему усмотрению. Git — это просто одна из самых широко используемых и поддерживаемых систем.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание статического веб-сайта в Azure из Visual Studio Code](tutorial-vscode-static-website-node-01.md)