---
title: Параметры конфигурации размещения веб-приложений
description: Узнайте, как задать общие конфигурации для веб-приложения.
ms.topic: conceptual
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: 271c2b916062d9cbd2b905fb937c9fb216eb43e5
ms.sourcegitcommit: 1901759f41adfac3c3f2ff135bcf72206543b639
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934310"
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

## <a name="configure-certificates"></a>Настройка сертификатов

Если для приложения немедленно требуются сертификаты, у вас есть несколько вариантов [их предоставления](/azure/app-service/configure-ssl-certificate#import-an-app-service-certificate):

* отправить собственный сертификат;
* управлять сертификатами в службе приложений;
* импортировать сертификат из Azure Key Vault;
* предоставить сертификат [в коде](/azure/app-service/configure-ssl-certificate-in-code).

## <a name="configure-secrets"></a>Настройка секретов

Секреты обычно предоставляются указанными ниже способами.

* Azure Key Vault — создайте ресурс для этой службы, которая предоставляет [секреты приложения](/azure/app-service/app-service-key-vault-references). 
* Параметры приложения — если вы ищете более легкие решения, можно предоставить секреты в качестве параметров приложения и ссылаться на них с помощью `process.env.VARNAME`. 

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

* Сведения по выполнению многих из этих шагов см. в статье о потоке разработки [комплексного приложения Node.js](/azure/developer/javascript/how-to/develop-nodejs-on-azure). 