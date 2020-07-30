---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 95dd432dfae61346c5a3129be12c534eba045c10
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401635"
---
### <a name="send-a-test-notification"></a>отправка проверочного уведомления.

1. Откройте новую вкладку в [Postman](https://www.postman.com/downloads/).

1. Задайте в качестве метода запроса **POST** и введите следующий адрес:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Если вы решили выполнить инструкции в разделе о [проверке подлинности клиентов с помощью ключа API](#authenticate-clients-using-an-api-key-optional), обязательно настройте заголовки запросов так, чтобы они содержали значение **apikey**.

   | Клавиши                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <ваш_ключ_API>                 |

1. Выберите значение **raw** (без форматирования) для параметра **Body** (Основной текст), затем выберите **JSON** в списке вариантов форматирования и добавьте содержимое-заполнитель **JSON**.

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Нажмите кнопку **Code** (Код), которая находится под кнопкой **Save** (Сохранить) в правом верхнем углу окна. Запрос должен выглядеть примерно так, как показано в следующем примере для **HTML** (в зависимости от того, включен ли заголовок **apikey**).

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Запустите приложение **PushDemo** на одной или обеих целевых платформах (**Android** и **iOS**).

    > [!NOTE]
    > При тестировании на **Android** убедитесь, что не включен режим **отладки**. В противном случае, если приложение развернуто путем его запуска, принудительно закройте его и запустите снова из средства запуска.

1. В приложении **PushDemo** нажмите кнопку **Register** (Регистрация).

1. Вернитесь в **[Postman](https://www.postman.com/downloads)** , закройте окно **Generate Code Snippets** (Создание фрагментов кода), если это еще не сделано, а затем нажмите кнопку **Send** (Отправить).

1. Убедитесь, что вы получили **200 ответов "ОК"** в **[Postman](https://www.postman.com/downloads)** , а в приложении отображается оповещение **ActionA action received** (Получено действие ActionA).  

1. Закройте приложение **PushDemo**, а затем снова нажмите кнопку **Send** (Отправить) в **[Postman](https://www.postman.com/downloads)** .

1. Убедитесь, что вы снова получили **200 ответов "ОК"** в **[Postman](https://www.postman.com/downloads)** . Проверьте, отображается ли в области уведомлений уведомление для приложения **PushDemo** с правильным сообщением.

1. Коснитесь уведомления и убедитесь, что при этом открывается приложение и отображается оповещение **ActionA action received** (Получено действие ActionA).

1. Вернувшись в **[Postman](https://www.postman.com/downloads)** , измените текст предыдущего запроса, чтобы отправить неотображаемое уведомление, указав для параметра **action** (действие) значение *action_b* вместо *action_a*.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Не закрывая приложение, нажмите кнопку **Send** (Отправить) в **[Postman](https://www.postman.com/downloads)** .

1. Убедитесь, что вы получили **200 ответов "ОК"** в **[Postman](https://www.postman.com/downloads)** , а в приложении отображается оповещение **ActionB action received** (Получено действие ActionB) вместо **ActionA action received** (Получено действие ActionA).

1. Закройте приложение **PushDemo**, а затем снова нажмите кнопку **Send** (Отправить) в **[Postman](https://www.postman.com/downloads)** .

1. Убедитесь, что вы получили **200 ответов "ОК"** в **[Postman](https://www.postman.com/downloads)** и что неотображаемое уведомление действительно не отображается в области уведомлений.
