---
title: Параметры конфигурации размещения веб-приложений
description: Узнайте, как задать общие конфигурации для веб-приложения.
ms.topic: conceptual
ms.date: 01/11/2021
ms.custom: devx-track-js
ms.openlocfilehash: ec183d08460b932cec1dea8d301f2877d7bc9ece
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561700"
---
# <a name="hosting-web-apps-on-azure"></a>Размещение веб-приложений в Azure

Узнайте, как задать общие конфигурации для веб-приложения. Если общий параметр отсутствует, сообщите о проблеме с помощью средства обратной связи. 

Все **обязательные параметры** запрашиваются при создании ресурса. Если в этот момент параметр не запрашивается, будет использоваться значение по умолчанию, которое можно изменить после создания ресурса. 

## <a name="what-is-a-web-app"></a>Сведения о веб-приложении

Веб-приложением является любое приложение, доступ к которому осуществляется с помощью URL-адреса в Интернете. Существует множество служб Azure, которые можно рассматривать как веб-приложения. Наиболее популярные службы, обычно используемые для веб-приложения:

* Служба приложений, которая также включает указанные ниже элементы.
    * [Статические веб-приложения](/azure/static-web-apps/)
    * [Функции](/azure/azure-functions/)
    * [Веб-приложения](/azure/app-service/)
    * [Контейнеры](/azure/app-service/configure-custom-container?pivots=container-linux)
* Контейнеры [Kubernetes](/azure/aks/) и отдельные [контейнеры](/azure/container-instances/).
* Виртуальные машины [Windows](/azure/virtual-machines/windows) и [Linux](/azure/virtual-machines/linux).

## <a name="how-to-configure-web-app-settings"></a>Настройка параметров веб-приложения

Большинство служб Azure предоставляют четыре указанных ниже способа настройки параметров.

* [Портал Azure](https://portal.azure.com)
* [Пакет Azure SDK](https://github.com/Azure/azure-sdk) для службы, обычно отмеченный в качестве средства управления.
* [Azure CLI](/cli/azure/)
* [Azure PowerShell](/powershell/azure/)

Многие параметры также можно настроить в Visual Studio Code с помощью [расширений](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). 

## <a name="use-default-domain-name-provided-by-azure"></a>Использование доменного имени по умолчанию, предоставляемого Azure

Большинство служб Azure предоставляют URL-адрес для ресурса. Имя службы определяет поддомен, а остальная часть домена поступает из Azure. 

Например:

* Функции Azure — `https://my-function-app.azurewebsites.net`
* Веб-приложение Azure — `https://my-web-app.azurewebsites.net`
* Azure Storage Blob — `https://mystorage.blob.core.windows.net/`

Некоторые службы, например статические веб-приложения, предоставляют поддомен, который является относительно уникальным, что позволяет использовать его сразу в рабочей среде:

* Статические веб-приложения Azure — `https://gentle-tree-0b08aaf12.azurestaticapps.net`

## <a name="configure-custom-domain-name"></a>Настройка имени личного домена 

Каждая служба предоставляет собственный механизм для добавления пользовательского домена. 

* [Статические веб-приложения Azure](/azure/static-web-apps/custom-domain)
* [Функции Azure](/azure/app-service/app-service-web-tutorial-custom-domain) & [веб-приложение Azure](/azure/app-service/app-service-web-tutorial-custom-domain) — функции создаются на основе веб-приложений, поэтому они используют один и тот же механизм.
* [Большие двоичные объекты службы хранилища Azure](/azure/storage/blobs/storage-custom-domain-name?tabs=azure-portal)

## <a name="configure-port-forwarding"></a>Настройка перенаправления портов

Необходимо [сопоставлять номер порта приложения](/azure/app-service/configure-language-nodejs?pivots=platform-windows#get-port-number), если он не является портом по умолчанию (`8080`). Это позволяет службе приложений перенаправлять запросы в правильный порт. 

## <a name="configure-browser-for-cors-to-connect-with-server"></a>Настройка браузера для подключения CORS к серверу

Если вам нужно подключиться к собственному серверу и при этом игнорировать защиту CORS во время выполнения и отладки на локальном клиенте, рекомендуется настроить этот параметр в файле отладки Visual Studio Code (`launch.json`), чтобы передать параметры в браузер для отключения защиты. 

Так как этот файл обязательно используется для запуска сеанса отладки, вы можете выполнить запись после изменений в систему управления версиями. 

### <a name="configure-edge-browser-to-disable-cors-for-debugging"></a>Настройка браузера Edge для отключения CORS для отладки

В следующем файле `launch.json` настраивается параметр **браузера Edge**, чтобы отключить защиту CORS для сеанса отладки: `--disable-web-security`. 

```json
{
    // Debug client, with requests to server, w/o 
    // changes to client or server
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Edge against localhost",
            "request": "launch",
            "type": "pwa-msedge",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}",
            "runtimeArgs": [
                "--disable-web-security"
            ],
        },
    ]
}
```

### <a name="configure-chrome-browser-to-disable-cors-for-debugging"></a>Настройка браузера Chrome для отключения CORS для отладки

Следующий файл `launch.json` настраивает параметр **браузера Chrome**, чтобы отключить защиту CORS для сеанса отладки: `--disable-web-security`. 

```json
{
    // Debug client, with requests to server, w/o 
    // changes to client or server
    "version": "0.2.0",
    "configurations": [
        {
            "type": "pwa-chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}",
            "runtimeArgs": [
                "--disable-web-security"
            ],
        }
    ]
}
```


## <a name="configure-certificates"></a>Настройка сертификатов

Если для приложения немедленно требуются сертификаты, у вас есть несколько вариантов [их предоставления](/azure/app-service/configure-ssl-certificate#import-an-app-service-certificate):

* отправить собственный сертификат;
* управлять сертификатами в службе приложений;
* импортировать сертификат из Azure Key Vault;
* предоставить сертификат [в коде](/azure/app-service/configure-ssl-certificate-in-code).

## <a name="configure-secrets"></a>Настройка секретов

Секреты обычно предоставляются указанными ниже способами.

* Azure Key Vault — создайте ресурс для этой службы, которая предоставляет [секреты приложения](/azure/app-service/app-service-key-vault-references). 
* Параметры приложения — если вы ищете более легкие решения, можно указать в качестве параметров приложения секреты и ссылаться на них с помощью типичных [переменных среды](/azure/app-service/configure-language-nodejs?pivots=platform-windows), `process.env.VARNAME`. 

## <a name="configure-logging"></a>Настройка журнала

Ведение журнала включает в себя:

* ведение журнала платформы — наблюдение за событиями вне приложения;
* ведение журнала приложений — наблюдение за событиями внутри приложения.

Журналы платформы позволяют выполнить следующие действия:
* анализировать работоспособность среды;
* выполнить масштабирование до другой ценовой категории или в разных регионах. 

Журналы приложений не предоставляются. Вы можете добавить собственный элемент ведения журнала для поведения внутреннего приложения:
* [Azure Monitor](/azure/azure-monitor/overview) предоставляет библиотеки npm для [Application Insights](/azure/azure-monitor/app/app-insights-overview), чтобы предоставить возможности ведения журнала и ресурс хранилища, в котором архивируются журналы. 

## <a name="configure-database-and-storage"></a>Настройка базы данных и хранилища

Как правило, подключение к базе данных или хранилищу данных начинается со строки подключения. 

Рекомендации по подключениям к данным:
* Используйте текущее подключение.
* Новое хранилище данных. Если для вашего приложения нужен новый механизм хранения, Azure предоставляет [множество различных вариантов выбора базы данных](integrate-database.md). Подключение должно быть безопасно сохранено. 

## <a name="missing-something"></a>Возникли вопросы? 

Если вы не нашли в списке то, что искали, отправьте отзыв, чтобы сообщить нам об этом. 

## <a name="next-steps"></a>Дальнейшие действия

* Сведения по выполнению многих из этих шагов см. в статье о потоке разработки [комплексного приложения Node.js](./develop-nodejs-on-azure.md).