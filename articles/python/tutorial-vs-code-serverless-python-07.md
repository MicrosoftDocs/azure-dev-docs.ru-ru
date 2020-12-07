---
title: Шаг 7. Добавление привязки к хранилищу для бессерверных Функций Azure на Python с помощью VS Code
description: Шаг учебника 7. Добавление в бессерверный код Python привязки для сохранения сообщений в службе хранилища Azure.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: aa0913f35915a4d4a97b1d0183976614509b704d
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442177"
---
# <a name="7-add-a-storage-binding-for-azure-functions-in-python"></a>7: Добавление привязки к хранилищу для решения "Функции Azure" на Python

[Предыдущий шаг: развертывание второй функции](tutorial-vs-code-serverless-python-06.md)

_Привязка_ позволяет подключать код функции к таким ресурсам, как служба хранилища Azure, без необходимости использовать дополнительный код для доступа к данным.

Привязка определяется в файле *function.json* и может представлять как входные, так и выходные данные. Функция может использовать несколько входных и (или) выходных привязок, но не более одного триггера. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](/azure/azure-functions/functions-triggers-bindings).

В этом разделе описано, как добавить привязку к хранилищу в функцию HttpExample, которую мы создали ранее с помощью этого руководства. Через эту привязку функция будет при каждом запросе сохранять сообщения в хранилище. Подключенное хранилище использует ту же учетную запись хранения по умолчанию, что и само приложение-функция. Но если вы предполагаете интенсивную нагрузку на хранилище, есть смысл создать для него отдельную учетную запись.

1. Сохраните удаленные параметры проекта Функций Azure в файле *local.settings.json*, открыв палитру команд и выбрав **Функции Azure: скачать удаленные параметры**.

    Откройте файл *local.settings.json* и убедитесь, что он содержит значение для `AzureWebJobsStorage`. Это строка подключения к учетной записи хранения.

1. В разделе **Локальный проект** в обозревателе Функций Azure щелкните функцию `HttpExample` правой кнопкой мыши и выберите **Добавить привязку**:

    ![Команда добавления привязки в обозревателе Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command-to-azure-functions-in-visual-studio-code.png)

1. В запросах, которые вам предложит Visual Studio Code, выберите или введите следующие значения:

    | prompt | Предоставляемое значение |
    | --- | --- |
    | Select binding direction (Выберите направление привязки) | out |
    | Выберите привязку с направлением "out" | Хранилище очередей Azure |
    | Имя, используемое для идентификации этой привязки в коде | msg |
    | Очередь, в которую будет отправлено сообщение | outqueue |
    | Выберите параметр из *local.settings.json* (запрос на подключение к хранилищу) | AzureWebJobsStorage |

1. Выбрав параметры, убедитесь, что в файл *local.settings.json* добавлена следующая привязка:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Теперь, когда привязка настроена, ее можно использовать в коде функции. Как вы уже знаете, новая привязка будет доступна в коде как аргумент функции `main` в файле *\_\_init\_\_.py*.

    Например, вы можете заменить текст файла *\_\_init\_\_.py* из примера HttpExample следующим текстом, где аргумент `msg` применяется для записи сообщения с меткой времени и именем, указанным в запросе. В комментариях поясняются конкретные изменения:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Чтобы локально протестировать эти изменения, снова запустите отладчик Visual Studio Code, нажав клавишу F5 или выбрав команду меню **Выполнить** > **Начать отладку**.

    Как и ранее, в **окне вывода** должны отобразиться конечные точки проекта.

1. В браузере откройте URL-адрес `http://localhost:7071/api/HttpExample?name=VS%20Code`, чтобы создать запрос к конечной точке HttpExample, после чего сообщение должно быть помещено в очередь.

1. Чтобы убедиться, что сообщение было записано в очередь с именем outqueue (как указано в привязке), можно использовать любой из следующих трех методов:

    1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, которая содержит проект функции. В этой группе ресурсов найдите и разверните учетную запись хранения для этого проекта, а затем выберите элемент **Очереди**. На открывшейся странице перейдите к элементу outqueue, где должны отображаться все сохраненные сообщения.

    1. Изучите содержимое очереди с помощью Обозревателя службы хранилища Azure, который интегрируется с Visual Studio, как описано в руководстве по [подключению Функций к Службе хранилища Azure с помощью Visual Studio Code](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code), в частности в разделе о [проверке очереди вывода](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code#examine-the-output-queue).

    1. С помощью Azure CLI создайте запрос к очереди хранилища, как описано в [этом разделе](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?pivots=programming-language-python).

1. Чтобы протестировать новый код в облаке, повторно разверните проект, используя команду **Развернуть в приложении-функции** в обозревателе **Azure: Функции**. Выберите созданное ранее приложение-функцию, если появится соответствующий запрос. По завершении развертывания (этот процесс займет несколько минут) в **окне вывода** снова отобразятся общедоступные конечные точки, с помощью которых вы сможете повторить тесты.

> [!div class="nextstepaction"]
> [Привязка хранилища добавлена — перейти к шагу 8 >>>](tutorial-vs-code-serverless-python-08.md)

[Возникли проблемы? Сообщите нам!](https://aka.ms/python-functions-qs-ms-survey)
