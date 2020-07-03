---
title: Запись бессерверного кода Node.js с помощью функций Azure
description: Руководство по использованию функций Azure для создания и развертывания бессерверного кода с помощью функций Azure.
ms.topic: article
ms.date: 08/19/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: ae6a4cebef39976af4d9a30534d394d37d86a0c8
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792267"
---
# <a name="use-azure-functions-to-write-serverless-nodejs-code-on-azure"></a>Использование Функций Azure для написания бессерверного кода Node.js в Azure

В Azure бессерверное предложение называется "Функции Azure". Бессерверный код позволяет создавать реагирующие конечные точки по запросу в Интернете не заботясь о подготовке инфраструктуры или управлении ею. Бессерверный код состоит из сценариев или других битов кода, которые выполняются в ответ на различные события. 

Сначала перейдите в раздел:

- [Создание первой функции с помощью Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code). В этой статье описано использование Функций Azure в контексте Visual Studio Code, чтобы упростить многие детали.

Затем расширьте свои знания о возможностях Функций Azure, просмотрев следующие статьи:

- [Общие сведения о Функциях Azure](/azure/azure-functions/functions-overview), где описаны преимущества разработки бессерверных приложений, затраты и различные триггеры, которые можно использовать для запуска бессерверного кода.

- [Основные понятия. Триггеры и привязки в функциях Azure](/azure/azure-functions/functions-triggers-bindings)

- [Руководство для разработчиков по Функциям Azure](/azure/azure-functions/functions-reference), за которыми следует [Руководство разработчика JavaScript для Функций Azure](/azure/azure-functions/functions-reference-node)

- Если вы заинтересованы в написании функций *с отслеживанием состояния* в бессерверной среде, ознакомьтесь со статьей [Что такое Устойчивые функции?](/azure/azure-functions/durable/durable-functions-overview) а также [Создание устойчивой функции с помощью JavaScript](/azure/azure-functions/durable/quickstart-js-vscode).

Здесь вы можете ознакомиться с другими ресурсами, которые помогут вам более подробно изучить бессерверный код:

- Модуль Microsoft Learn: [Обеспечение автоматических обновлений в веб-приложении с помощью Функций Azure и службы SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/)

- Сведения об использовании различных триггеров для выполнения бессерверного кода:

  - [Выполнение кода по таймеру](/azure/azure-functions/functions-create-scheduled-function)
  - [Выполнение кода при отправке или обновлении файлов в хранилище BLOB-объектов Azure](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
  - [Выполнение кода при записи сообщения в Хранилище очередей Azure](/azure/azure-functions/functions-create-storage-queue-triggered-function)

- [Хранение неструктурированных данных с помощью функций Azure и Azure Cosmos DB](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=javascript). Сведения о других базах данных см. в статьях [Интеграция баз данных Azure в код Node.js](node-howto-integrate-databases.md)

- [Как программировать и тестировать функции Azure в локальной среде](/azure/azure-functions/functions-develop-local)

- [Методика тестирования кода с помощью Функций Azure](/azure/azure-functions/functions-test-a-function) и [обработка ошибок](/azure/azure-functions/functions-bindings-error-pages)

- [Настройка проверки подлинности в Azure Active Directory](/azure/app-service/configure-authentication-provider-aad?toc=%2fazure%2fazure-functions%2ftoc.json)

- [Настройка непрерывного развертывания в Azure Pipelines](/azure/azure-functions/functions-how-to-azure-devops)

- [Примеры использования Node.js + Функции Azure](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)