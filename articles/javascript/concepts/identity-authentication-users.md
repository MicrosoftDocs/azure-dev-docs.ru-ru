---
title: 'Проверка подлинности и авторизация: JavaScript, Azure'
description: Узнайте, как разрабатывать приложения JavaScript, в которых реализованы механизмы идентификации и проверки подлинности, а также учетные записи пользователей, с помощью Azure.
ms.topic: conceptual
ms.date: 12/09/2020
ms.custom: devx-track-js
ms.openlocfilehash: 68f53743d001b44aa1495ece36d0c2764ec749d4
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118466"
---
# <a name="identity-authentication-and-users"></a>Идентификация, проверка подлинности и пользователи

Проверка подлинности и авторизация — это обширные темы в контексте веб-приложений, которые можно сократить до конкретных программных задач и операций взаимодействия пользователей с приложением. Эта статья посвящена основным понятиям, представление о которых обычно нужно иметь разработчику JavaScript. 

## <a name="authentication-with-azure"></a>Проверка подлинности с использованием Azure

Проверка подлинности — это возможность идентификации для получения доступа к объекту. 

Как правило, для разработчика JavaScript в Azure это означает возможность:

* предоставлять программе доступ к ресурсам Azure;
* разрешить пользователю доступ к приложению, как правило, после входа в систему.

|Обязательно|Перспектива|Описание|
|--|--|--|
|Да|Разработчик|Для доступа к ресурсам Azure код приложения должен передавать в Azure необходимые учетные данные.|
|Нет|User (Пользователь)|Для пользователя приложения проверка подлинности может быть анонимной или требовать указания учетной записи пользователя. Для этого ограниченного доступа можно использовать любой распространенный поставщик проверки подлинности, включая Майкрософт. Или же вы можете создать собственный уровень проверки подлинности для своих пользователей.|

## <a name="authentication-for-developers-to-azure-services"></a>Проверка подлинности в службах Azure для разработчиков

Для программной проверки подлинности в Azure требуются допустимые учетные данные для конкретной службы, используемой кодом. Вам нужно ознакомиться с кратким руководством по работе со службой и определить тип учетных данных, которые для нее требуется предоставить. 

### <a name="local-developer-environment-for-authenticating-to-azure"></a>Локальная среда разработки для проверки подлинности в Azure

Когда вы узнаете, как подключиться к службе, создайте субъект-службу и задайте его как переменную среды на компьютере разработки. Это позволяет оградить вашу личную учетную запись от прямого взаимодействия с Azure и устранить риск ее компрометации из-за записи учетных данных в исходный код. 

### <a name="cloud-apps-authenticating-to-azure"></a>Проверка подлинности для облачных приложений в Azure

Для облачных приложений службы размещения Azure предоставляют доступ к [параметрам приложений](../how-to/configure-web-app-settings.md), включая переменные среды и секреты. Чтобы добавить в веб-приложение дополнительный уровень безопасности, храните секреты в [Azure Key Vault](/azure/key-vault) и получайте к ним доступ программными средствами из размещенного приложения. 

## <a name="modern-programmatic-authentication-with-azureidentity"></a>Современная программная проверка подлинности с использованием @azure/identity

В текущей библиотеке Azure SDK используется субъект-служба для программной проверки подлинности в службах Azure с помощью пакета npm [@azure/identity](https://www.npmjs.com/package/@azure/identity). Эта проверка подлинности упрощает процесс и доступна в [современных пакетах Azure SDK](https://www.npmjs.com/package/@azure/identity#client-libraries-supporting-authentication-with-azure-identity). 

```javascript
// The default credential first checks environment variables for configuration.
// If environment configuration is incomplete, it will try managed identity.

// Azure service to use
const { KeyClient } = require("@azure/keyvault-keys");

// Azure authentication library to access Azure service
const { DefaultAzureCredential } = require("@azure/identity");

// Azure SDK clients accept the credential as a parameter
const credential = new DefaultAzureCredential();

// Create authenticated client
const client = new KeyClient(vaultUrl, credential);

// Use service from authenticated client
const getResult = await client.getKey("MyKeyName");
```

## <a name="classic-programmatic-authentication"></a>Классическая программная проверка подлинности

Для большинства других поддерживаемых библиотек Azure SDK используйте один из следующих пакетов: 

* [@azure/ms-rest-js](https://www.npmjs.com/package/@azure/ms-rest-js) — позволяет работать в среде браузера и Node.js;
* [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) — предоставляет несколько различных механизмов проверки подлинности, включая интерактивную, основанную на субъекте-службе и реализуемую с помощью учетной записи пользователя и пароля;
* [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth) — требует приложения Azure AD.

В приведенном ниже примере демонстрируется проверка подлинности с использованием ключа и конечной точки, предоставленных службой.

```javascript
// Azure service to use
const { QnAMakerRuntimeClient } = require("@azure/cognitiveservices-qnamaker-runtime");

// Azure authentication library to access Azure service
const { CognitiveServicesCredentials } = require("@azure/ms-rest-azure-js");  
 
// QnA Maker runtime credentials
const QNAMAKER_KEY = process.env["QNAMAKER_KEY"];
const QNAMAKER_ENDPOINT = process.env["QNAMAKER_ENDPOINT"];
const KNOWLEDGEBASE_ID = process.env["QNAMAKER_KNOWLEDGE_BASE_ID"];

const cognitiveServicesCredentials = new CognitiveServicesCredentials(QNAMAKER_KEY);
const client = new QnAMakerRuntimeClient(cognitiveServicesCredentials, QNAMAKER_ENDPOINT);
const customHeaders = { Authorization: `EndpointKey ${QNAMAKER_KEY}` };

// A question you'd like to get a response for, from the knowledge base. For example
const question = "How are you?";

// Maximum number of answer to retreive
const top = 1;

// Find only answers that contain these metadata
const strictFilters = [{ name: "editorial", value: "chitchat" }];

client.runtime.generateAnswer( 
        KNOWLEDGEBASE_ID,
        { question, top, strictFilters },
        { customHeaders }
).then(result =>{
    console.log(JSON.stringify(result));

    // Sample Result
    // {
    //   answers: [
    //     {
    //       questions: [
    //         "How are you?",
    //         "How is your tuesday?"
    //       ],
    //       answer:
    //         ""I'm doing great, thanks for asking!",
    //       score: 100,
    //       id: 90,
    //       source:
    //         "qna_chitchat_Friendly.tsv",
    //       metadata: [{ name: "editorial", value: "chitchat" }],
    //       context: { isContextOnly: false, prompts: [] }
    //     }
    //   ],
    //   debugInfo: null,
    //   activeLearningEnabled: false
    // }

});

```

## <a name="user-authentication-with-an-app-registration"></a>Проверка подлинности пользователей путем регистрации приложений

Библиотека проверки подлинности Майкрософт (MSAL) — рекомендуемая библиотека для веб-разработки решений для проверки подлинности пользователей. Библиотека доступна на нескольких [языках и платформах](/azure/active-directory/develop/msal-overview#languages-and-frameworks).

Чтобы использовать MSAL, веб-приложение нужно [зарегистрировать](/azure/active-directory/develop/quickstart-register-app) в Майкрософт. Регистрация приложения включает указание общих сведений о проверке подлинности, таких как разрешения для области пользователя и URL-адрес перенаправления. 

Дополнительные сведения см. в примере проекта в этом [кратком руководстве по MSAL](/azure/active-directory/develop/quickstart-v2-javascript).

Пользователю предоставляется разрешение на доступ к приложению при входе в него. Это разрешение хранится вместе с учетной записью пользователя. Можно управлять этими разрешениями и отзывать их:

* управление разрешениями приложения потребителя — [https://account.live.com/consent/manage](https://account.live.com/consent/manage);
* управление разрешениями приложения Active Directory — [https://myapplications.microsoft.com/](https://myapplications.microsoft.com/).

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка Службы приложений Azure](../how-to/configure-web-app-settings.md)