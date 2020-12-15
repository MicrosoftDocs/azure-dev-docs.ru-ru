---
title: Выбор средств JavaScript в Azure
description: Установка отдельных средств для разработки приложений Node.js и JavaScript в Azure
ms.topic: reference
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 714d096e4afde345bffa2582026f28fa2126a38c
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857800"
---
# <a name="tools-for-javascript-developers-on-azure"></a>Средства Azure для разработчиков JavaScript 

JavaScript — это экосистема многих средств. В этой статье описан набор средств, созданных и поддерживаемых корпорацией Майкрософт, для разработчиков JavaScript. Средства позволяют усовершенствовать новые службы Azure, а также сценарии развертывания и размещения. 

Вы не должны использовать эти средства, чтобы иметь возможность работать в Azure. Они просто значительно улучшают работу с точки зрения функциональности и поддержки. 

## <a name="azure-portal"></a>Портал Azure

[Портал Azure](https://portal.azure.com/) предоставляет доступ ко всем подпискам и ресурсам для вашей учетной записи. 

## <a name="azure-cli"></a>Azure CLI
Azure CLI оптимизирован для управления ресурсами Azure из командной строки. 

Azure CLI предоставляет указанные ниже сценарии использования.

* [Локальная установка](/cli/azure/install-az-cli2).
* [Веб-оболочка](https://shell.azure.com/).
* [Контейнер](/cli/azure/run-azure-cli-docker)

При использовании портала Azure доступ к Azure CLI можно получить в верхней панели навигации.

:::image type="content" source="media/azure-tools/azure-portal-select-azure-cloud-shell.png" alt-text="При использовании портала Azure доступ к Azure CLI можно получить в верхней панели навигации.":::

## <a name="typescript"></a>TypeScript

[TypeScript](https://www.typescriptlang.org/download) предоставляет все функции JavaScript и дополнительный уровень. Это — система типов TypeScript. Существующий рабочий код JavaScript также является кодом TypeScript. Основным преимуществом TypeScript является выделение кода, который может вести себя непредвиденным образом, что снижает вероятность возникновения ошибок.

## <a name="typescript-and-the-azure-sdk-client-libraries"></a>TypeScript и клиентские библиотеки пакета Azure SDK

Справочная документация по клиентской библиотеке пакета Azure SDK написана для TypeScript, так как клиентские библиотеки записываются с помощью TypeScript. Чтобы пользоваться клиентскими библиотеками пакета Azure SDK, не нужно использовать TypeScript. 

Ознакомьтесь с [рекомендациями по TypeScript для Azure SDK](https://azure.github.io/azure-sdk/typescript_introduction.html).

## <a name="visual-studio-code"></a>Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com) является предпочтительной интегрированной средой для разработки JavaScript в Azure. Интерфейс, функции и расширения взаимодействуют, чтобы сократить время разработки и упростить ее. 

Создайте рабочую область проекта в корне локального проекта разработки, а затем добавьте все соответствующие конфигурации, параметры и расширения. Верните файл рабочей области с проектом, чтобы у каждого члена группы был доступ к параметрам и средствам, необходимым для проекта.

Используя Visual Studio Code вы получите следующие преимущества:

* Visual Studio Code отображает встроенную справочную документацию Azure.
* Visual Studio Code предоставляет возможность завершения операторов.
* Несколько неоднозначных типов и объектов.

Visual Studio Code предоставляет обширную документацию по [использованию проекта JavaScript](https://code.visualstudio.com/docs/nodejs/working-with-javascript). 

## <a name="visual-studio-code-extensions"></a>Расширения Visual Studio Code
Используйте следующие бесплатные расширения для взаимодействия со службами Azure непосредственно в Visual Studio Code.

| Инструмент | Description  |
|:---------:|---------|
| [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions "Ссылка на расширение Функций Azure") <br> [![Средства Функций Azure](media/node-azure-tools/icon-azure-functions.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) | Создание, администрирование, просмотр, отладка и развертывание функций|
| [Служба приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice "Ссылка на расширение Службы приложений Azure") <br> [![Средства службы приложений](media/node-azure-tools/icon-azure-app-service.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) | Навигация по сайтам и порталу Azure, создание сайтов и развертывание в слоты |
| [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb "Ссылка на расширение Cosmos DB" )  <br> [![Средства Cosmos DB](media/node-azure-tools/icon-cosmos-db.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)| Создание, просмотр и обновление распределенных многомодельных баз данных в Azure |
| [Docker](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)   <br> [![Docker](media/node-azure-tools/icon-docker.png)](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)| Управление контейнерами Docker и образами, Docker Hub и реестром контейнеров Azure |

> [!div class="nextstepaction"]
> [Получите больше расширений Azure в Visual Studio Code Marketplace](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance)
