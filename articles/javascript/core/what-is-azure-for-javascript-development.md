---
title: Что такое Azure для разработчиков JavaScript
description: Основные понятия, связанные с Azure, для разработчиков JavaScript, TypeScript и Node.js.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: 155add5f2c1119f028cfae966438bda4ead3e758
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97601005"
---
# <a name="what-is-azure-for-javascript-developers"></a>Что такое Azure для разработчиков JavaScript

Azure — это облачная платформа, предоставляющая полный спектр вариантов размещения и облачных служб. Если вы не знакомы с разработкой в облаке, узнайте больше об Azure:

* [Центр архитектуры Azure](/azure/architecture/) 
* [Терминология Azure](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts)
* [10 принципов проектирования приложений Azure](/azure/architecture/guide/design-principles/)
* [Конструктивные шаблоны облачных решений](/azure/architecture/patterns/)

## <a name="javascript-typescript-and-other-languages"></a>JavaScript, TypeScript и другие языки

Среда выполнения Azure поддерживает JavaScript, а также TypeScript, как и любые другие разновидности, которые компилируются в код на JavaScript. 

## <a name="run-javascript-with-hosted-runtime"></a>Запуск JavaScript с использованием размещенной среды выполнения 

* [Служба приложений Azure](/azure/app-service/) использует обработчик среды выполнения Node.js. Чтобы отобразились сведения обо всех поддерживаемых версиях Node.js, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

    ```azurecli-interactive
    az webapp list-runtimes | grep node
    ```

* [Функции Azure, поддерживаемые версиями Node.js](/azure/azure-functions/functions-reference-node?tabs=v2#node-version), основаны на используемой версии Функций. 

* Пользовательские среды выполнения — пользовательская среда выполнения поддерживается следующим образом:

    * [Виртуальные машины](/azure/virtual-machines/)
    * контейнеры — [экземпляры](/azure/container-instances/), [веб-приложения](/azure/app-service/), [Kubernetes](/azure/aks/);
    * функции (бессерверные) — используйте [пользовательские обработчики](/azure/azure-functions/functions-custom-handlers).

## <a name="run-javascript-with-azure-sdks"></a>Выполнение JavaScript с помощью пакетов Azure SDK

Все пакеты Azure SDK запускаются с помощью JavaScript без любых других средств. Хотя большинство современных пакетов SDK написаны на TypeScript и предоставляют файл `*.d.ts` для проверки типов, TypeScript не является обязательным требованием для использования пакетов Azure SDK или облачных служб Azure. 

Код JavaScript может использовать службы Azure, независимо от того, где они размещены (в локальной, гибридной или облачной среде). Рекомендуемый способ работы со службами Azure программными средствами с JavaScript — использовать пакеты SDK Azure. Для этих пакетов SDK версия среды выполнения Node.js должна быть не ниже 8. 

## <a name="azure-concepts-and-terminology"></a>Основные понятия и терминология, связанные с Azure

Чтобы начать работу с Azure:
* Создайте [бесплатную подписку](https://azure.microsoft.com/en-us/free/).
* Создайте ресурс, что предусматривает:
    * вход на [портал Azure](https://portal.azure.com/);
    * создание группы ресурсов для хранения логической коллекции ресурсов Azure;
    * создание ресурса в группе ресурсов.
* Используйте ресурс в зависимости от его типа, например:
    * С помощью портала Azure
    * С помощью Azure CLI
    * С помощью PowerShell
    * с помощью пакетов Azure SDK.

См. дополнительные сведения о [вводных понятиях и терминологии, связанных с Azure](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts). 

Azure предоставляет рекомендации, связанные с [соглашениями об именовании и расстановке тегов ресурсов](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging), чтобы помочь вам найти нужные ресурсы. 

## <a name="select-development-ide-and-set-up-environment"></a>Выбор интегрированной среды разработки и настройка среды

Разрабатывайте приложения JavaScript с помощью любой интегрированной среды разработки. Для разработки JavaScript мы рекомендуем использовать **Visual Studio Code**. 

См. дополнительные сведения о [рекомендуемых средствах для разработчиков JavaScript](../node-azure-tools.md). 

## <a name="select-azure-account-subscription-resource-and-tag"></a>Выбор учетной записи, подписки, ресурса и тега Azure

* Ваша **учетная запись Azure** связана через адрес электронной почты с подпиской. У вас может быть одна или несколько учетных записей Azure.

* **Подписка** — это организационная и расчетная единица для ресурсов. У вас может быть одна или несколько подписок. См. дополнительные сведения о [создании исходных рабочих подписок](/azure/cloud-adoption-framework/ready/azure-best-practices/initial-subscriptions).

* **Группа ресурсов** — это следующий уровень организации в подписке. Создайте новую группу ресурсов для каждого проекта или группы служб Azure, которые вы планируете использовать вместе. 

* При выборе имени **ресурса** важно знать, когда оно используется в качестве конечной точки URL-адреса ресурса. Кроме того, можно назначить имя личного домена нескольким ресурсам после создания ресурса. 

* Более гибкое группирование ресурсов заключается в **расстановке тегов ресурсов** с использованием имени и значения свойства. Обычно теги используются, чтобы добавить имя или адрес электронной почты для владельца проекта для каждого создаваемого ресурса. Администратор подписки может легко найти ресурс, которым вы владеете, или определить владение при возникновении проблем с использованием или выставлением счетов. 

[Систематическое соглашение об именовании](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming) — это важная часть использования Azure на рабочем уровне.

## <a name="create-azure-resources"></a>Создание ресурсов Azure

Наиболее распространенный способ создания ресурсов для нового пользователя в Azure — использовать портал Azure. В ходе этого процесса отображаются все варианты, которые необходимо выбрать, наряду с рекомендуемыми вариантами.

### <a name="pricing-tiers"></a>Ценовые категории

Ценовая категория — это то, как оплачивается ваш ресурс. Используйте [калькулятор цен Azure](https://azure.microsoft.com/en-us/pricing/calculator), чтобы ознакомиться с процессом выставления счетов за ресурс. 

### <a name="free-tier-resources"></a>Ресурсы уровня "Бесплатный"

При выборе ценовой категории "Бесплатный" (F0) важно понимать ограничения, связанные с этим планом.

Когда предлагается уровень "Бесплатный":

* Подписка может быть ограничена одним бесплатным ресурсом этой службы. Если вы не можете создать бесплатный ресурс, это означает, что в вашей подписке такой ресурс уже есть.
* При превышении квоты ценовой категории в транзакциях в секунду или транзакциях в месяц приложение получит ошибку HTTP с соответствующим сообщением. 

### <a name="resource-creation-time"></a>Время создания ресурса

Создание большинства ресурсов занимает несколько минут, но в некоторых случаях это может быть и несколько минут. В окне уведомлений на портале Azure отображаются сведения о доступности ресурса для использования и предоставляется ссылка на ресурс. 

## <a name="install-azure-sdk-for-resources"></a>Установка пакета Azure SDK для ресурсов

Использование [**пакетов Azure SDK**](../azure-sdk-library-package-index.md) — это рекомендуемый программный способ работы с ресурсами для разработчиков JavaScript. 

С ресурсами Azure также можно работать:
* с помощью [Azure Cloud Shell](/cli/azure/install-azure-cli) или [Azure CLI](https://shell.azure.com/);
* [Azure PowerShell](/powershell/azure/?view=azps-5.2.0&preserve-view=true)
* [REST API](/rest/api/azure/);

## <a name="deploy-web-apps-to-hosting-options"></a>Развертывание веб-приложений в вариантах размещения

Варианты размещения позволяют быстро использовать Azure для вашего приложения. См. следующие краткие руководства и материалы по размещению для работы с Azure:

* Клиентское или статическое приложение:
    * [Vanilla JS](/azure/static-web-apps/getting-started?tabs=vanilla-javascript);
    * [React](/azure/static-web-apps/getting-started?tabs=react)
    * [Angular](/azure/static-web-apps/getting-started?tabs=angular)
    * [Vue](/azure/static-web-apps/getting-started?tabs=vue)
* Серверное приложение: 
    * [Развертывание приложения MongoDB на платформе Express.js в Службе приложений с помощью Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)
* Приложение-контейнер 
    * [Развертывание контейнерного приложения Express.js в Службе приложений для частного реестра контейнеров с использованием Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md?tabs=bash)
* Приложение виртуальной машины:
    * [Создание и развертывание виртуальной машины Linux с приложением Express.js с помощью Azure CLI и GitHub Actions](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md)

Узнайте о других [вариантах размещения](../how-to/deploy-web-app.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Установка Node.js](install-nodejs-develop-azure-sdk-project.md)
* [Узнайте о рекомендуемых средствах для разработчиков Azure JavaScript](../node-azure-tools.md)
