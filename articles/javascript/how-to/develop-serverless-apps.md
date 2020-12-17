---
title: Разработка бессерверного кода Node.js с помощью Функций Azure
description: Функции Azure предоставляют инфраструктуру бессерверного кода, которая позволяет создавать быстро реагирующие конечные точки HTTP по запросу.
ms.topic: how-to
ms.date: 10/27/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, contperf-fy21q2
ms.openlocfilehash: b90837cdb144e433a7d776e571291ff09610a845
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522299"
---
# <a name="use-azure-functions-to-develop-nodejs-serverless-code"></a>Использование Функций Azure для разработки бессерверного кода Node.js в Azure

Функции Azure предоставляют инфраструктуру бессерверного кода, которая позволяет создавать быстро реагирующие конечные точки HTTP по запросу. Бессерверный код состоит из кода JavaScript или TypeScript, который выполняется в ответ на различные события. 

Функции выполняются поверх веб-службы в виде кода или контейнера Docker, позволяя вам сосредоточиться на коде конечной точки. Функции также позволяют активировать другую функцию, чтобы рабочий поток функции мог заменить существующие возможности размещенного внутреннего сервера и устранить необходимость в управлении этим сервером. 

## <a name="what-is-a-function-resource"></a>Что такое ресурс Функций?

Ресурс Функций Azure — это логическая единица для всех связанных функций в одном географическом расположении Azure. Ресурс может содержать одну или несколько функций, которые могут быть независимыми друг от друга, а также связанными с входными или выходными триггерами. Вы можете выбрать существующие функции или создать собственные.

:::image type="content" source="../media/howto-serverless/portal-screenshot-new-azure-function-type.png" alt-text="Вы можете выбрать существующие функции или создать собственные.":::

К параметрам ресурсов Функций относятся типичные бессерверные конфигурации, которые определяют переменные среды, аутентификацию, ведение журнала и CORS.  

## <a name="durable-stateful-functions"></a>Устойчивые функции с отслеживанием состояния 

[Устойчивые функции](/azure/azure-functions/durable/durable-functions-overview) сохраняют *состояние* или управляют долго выполняющимися функциями в Azure. [Создайте устойчивую функцию с помощью JavaScript.](/azure/azure-functions/durable/quickstart-js-vscode)

## <a name="static-web-apps-include-functions"></a>Статические веб-приложения включают функции 

При разработке статического интерфейсного клиентского приложения (например, Angular, React или Vue), которые также требуют бессерверные API, используйте [статические веб-приложения](/azure/static-web-apps/getting-started?tabs=react) с функциями. 

## <a name="a-simple-javascript-function-for-http-requests"></a>Простая функция JavaScript для HTTP-запросов

Функция — это экспортированная асинхронная функция со сведениями о запросе и контексте. На следующем снимке портала Azure показан код функции. 

:::image type="content" source="../media/howto-serverless/portal-screenshot-azure-function-http.png" alt-text="Снимок экрана: Функция Azure на портале Azure.":::

## <a name="develop-functions-locally-with-visual-studio-code-and-extensions"></a>Локальная разработка функций с помощью Visual Studio Code и расширений

Создайте [первую функцию](/azure/azure-functions/functions-create-first-function-vs-code) с помощью Visual Studio Code. Visual Studio Code упрощает многие аспекты разработки благодаря [расширению Функций Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

Это расширение позволяет создавать функции JavaScript и TypeScript на основе распространенных шаблонов. 

Пример функции HTTP для Azure на JavaScript: 

```nodejs
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

Пример функции HTTP для Azure на TypeScript: 

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };

};

export default httpTrigger;
```

## <a name="configuring-the-function"></a>Настройка функции

Функции настраиваются с помощью файла **function.json**. Эта конфигурация позволяет настроить способ активации функции ("direction": in) и возвращаемый функцией результат ("direction": out), а также задать переменные среды и указать другие необходимые сведения для работы функции. См. дополнительные сведения о [триггере и привязке](/azure/azure-functions/functions-triggers-bindings?tabs=javascript.md). 

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

## <a name="develop-functions-remotely-using-the-azure-portal"></a>Удаленная разработка функций с помощью портала Azure

При [создании функции Azure на портале Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp) вы можете настроить функцию, написать код в предварительно заполненном шаблоне и протестировать функцию. 

На портале создаются только функции JavaScript, но не TypeScript. Чтобы выполнить разработку с помощью TypeScript, скачайте функцию или создайте функцию локально в Visual Studio Code с помощью расширения Функций. 

## <a name="next-steps"></a>Дальнейшие действия

Хорошей отправной точкой является [руководство по Функциям Azure для разработчика JavaScript](/azure/azure-functions/functions-reference-node). 

Из модуля Microsoft Learn можно узнать, как [включить автоматические обновления в веб-приложении с помощью Функций Azure и Службы SignalR](/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/).

* [Выполнение кода по таймеру](/azure/azure-functions/functions-create-scheduled-function)
* [Выполнение кода при отправке или обновлении файлов в хранилище BLOB-объектов Azure](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
* [Выполнение кода при записи сообщения в Хранилище очередей Azure](/azure/azure-functions/functions-create-storage-queue-triggered-function)
* [Хранение неструктурированных данных с помощью Azure Cosmos DB и службы "Функции Azure"](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=javascript)
* [Примеры использования Node.js + Функции Azure](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)
