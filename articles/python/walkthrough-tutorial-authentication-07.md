---
title: Пошаговое руководство. Часть 7. Аутентификация приложений Python в службах Azure
description: Изучение кода конечной точки API основного приложения, которая использует сторонние конечные точки API и записывает сообщение в Хранилище очередей Azure.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e026eca0216147c6614582e0cd070cee81daf99c
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379526"
---
# <a name="part-7-main-application-api-endpoint"></a>Часть 7. Конечная точка API основного приложения

[Предыдущая часть. Код запуска основного приложения](walkthrough-tutorial-authentication-06.md)

API */api/v1/getcode* приложения создает ответ JSON, содержащий буквенно-цифровой код и метку времени.

Сначала декоратор `@app.route` сообщает Flask о том, что функция `get_code` обрабатывает запросы к URL-адресу */api/v1/getcode*.

```python
@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
```

Затем мы вызываем сторонний API, URL-адрес которого находится в `number_url`, предоставляя ключ доступа, который мы получаем из хранилища ключей в заголовке.

```python
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code
```

Свойство `x-functions-key` в заголовке определяет способ отображения ключа доступа в заголовке для Функций Azure (где развернут этот пример API стороннего производителя). (Дополнительные сведения см. в разделе [Ключи доступа к функции](/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys).) Если вызов API по какой-либо причине завершается сбоем, мы возвращаем сообщение об ошибке и код состояния.

Предполагая, что вызов API возвращает числовое значение, мы создаем более сложный код, используя это число и случайные символы (с помощью нашей функции `random_char`).

```python
    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }
```

Переменная `code` здесь содержит полный ответ JSON для API приложения, который содержит значение кода, а также метку времени. Пример ответа будет таким: `{"code":"ojE-161-pTv","timestamp":"2020-04-15 16:54:48.816549"}`.

Но перед возвратом этого ответа мы записываем сообщение с ним в очередь хранилища, используя метод [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-) клиента очереди:

```python
    queue_client.send_message(code)

    return jsonify(code)
```

## <a name="processing-queue-messages"></a>Обработка сообщений очереди

Сообщения, хранящиеся в очереди, можно просматривать и администрировать с помощью [портала Azure](/azure/storage/queues/storage-quickstart-queues-portal#view-message-properties) или команды [`az storage message get`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-get) Azure CLI. Пример репозитория включает скрипт (*test.cmd* и *test.sh*), который запрашивает код из конечной точки приложения и проверяет очередь сообщений. Есть также скрипт для очистки очереди с помощью команды [`az storage message clear`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-clear).

Как правило, для приложения, как в этом примере, будет выполняться другой процесс, который асинхронно извлекает сообщения из очереди для дальнейшей обработки. Как упоминалось ранее, ответ, создаваемый этой конечной точкой API, может использоваться где угодно в приложении с применением двухфакторной проверки подлинности пользователя. В этом случае приложение должно сделать код недействительным после определенного периода времени, скажем, 10 минут. Простой способ выполнения этой задачи — хранить таблицу допустимых кодов двухфакторной проверки подлинности, которые используются в процедуре входа пользователя. В приложении будет выполняться простой процесс наблюдения за очередями с использованием следующей логики (в псевдокоде):

<pre>
pull a message from the queue and retrieve the code.

if (code is already in the table):
    remove the code from the table, thereby invalidating it
else:
    add the code to the table, making it valid
    call queue_client.send_message(code, visibility_timeout=600)
</pre>

В этом псевдокоде используется необязательный параметр `visibility_timeout` метода [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-), который определяет количество секунд до того, как сообщение станет видимым в очереди. Так как время ожидания по умолчанию равно нулю, сообщения, изначально записанные конечной точкой API, немедленно становятся видимыми для процесса наблюдения за очередями. В итоге этот процесс сразу же сохраняет их в таблице допустимого кода. При повторном включении того же сообщения в очередь с определенным временем ожидания процессу известно, что он получит код еще раз через 10 минут, после чего этот код будет удален из таблицы.

## <a name="implementing-the-main-app-api-endpoint-in-azure-functions"></a>Реализация конечной точки API основного приложения в Функциях Azure

Код, приведенный ранее в этой статье, использует веб-платформу Flask для создания конечной точки API. Так как Flask выполняется на веб-сервере, такой код нужно развернуть в Службе приложений Azure или на виртуальной машине.

Альтернативный вариант развертывания — бессерверная среда Функций Azure. В этом случае весь код запуска и код конечной точки API будут содержаться в той же функции, которая привязана к триггеру HTTP. Как и в случае со Службой приложений, вы используете [параметры приложения функции](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings) для создания переменных среды для кода.

Одной из частей реализации, которая становится проще, является проверка подлинности с помощью Хранилища очередей. Вместо получения объекта `QueueClient` с помощью URL-адреса очереди и объекта учетных данных вы можете создать *привязки хранилища очередей* для функции. Привязка обрабатывает весь процесс проверки подлинности в фоновом режиме. При использовании такой привязки функция получает готовый к использованию клиентский объект в качестве параметра. Дополнительные сведения и пример кода см. в статье [Подключение Функций Azure к службе хранилища Azure с помощью средств командной строки](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?tabs=bash%2Cbrowser&pivots=programming-language-python).

## <a name="next-steps"></a>Дальнейшие шаги

Из этого примера вы узнали, как приложения проходят проверку подлинности с помощью других служб Azure и как они могут использовать Azure Key Vault для хранения других необходимых секретов для сторонних API.

Шаблон, используемый здесь с Azure Key Vault и службой хранилища Azure, применим ко всем другим службам Azure. Важным этапом является установка правильных разрешений роли для приложения на странице этой службы на портале Azure или с помощью Azure CLI. (См. статью [Как назначить разрешения роли удостоверению приложения или субъекту-службе](how-to-assign-role-permissions.md).) Обязательно просмотрите документацию по службе, чтобы узнать, нужно ли настраивать другие политики доступа.

Помните, что вам нужно назначить те же роли и политики доступа субъекту-службе, используемому для локальной разработки.

Вкратце, инструкции из этого пошагового руководства вы можете применять к любым другим службам Azure и внешним службам.

Одна из тем, которые мы не рассматривали здесь, — проверка подлинности *пользователей*. Сведения об этих аспектах для веб-приложений см. в статье [Руководство. Сквозная проверка подлинности и авторизации в Службе приложений Azure](/azure/app-service/tutorial-auth-aad?pivots=platform-linux).

## <a name="see-also"></a>См. также раздел

- [Как аутентифицировать и авторизовать приложения Python в Azure](azure-sdk-authenticate.md)
- Пример для пошагового руководства: [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication)
- [Документация Azure Active Directory](/azure/active-directory)
- [Документация по Azure Key Vault](/azure/key-vault)
