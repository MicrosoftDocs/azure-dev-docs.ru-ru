---
title: Аутентификация с использованием модулей управления Azure для Node.js
description: Аутентификация с помощью субъекта-службы в модулях управления Azure для Node.js
ms.topic: how-to
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 413357533d5ddf8e41bc2e33d929074df4f2ac12
ms.sourcegitcommit: 84f64dec74b4b041b8830a4e7489e22f0e943440
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97864259"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Проверка подлинности с использованием модулей управления Azure для JavaScript

Все [клиентские библиотеки пакета SDK](../azure-sdk-library-package-index.md) требуют аутентификации через объект `credentials`. Существует несколько способов выполнить проверку подлинности и создать требуемые учетные данные.

Проверка подлинности, как и все программные обеспечения и службы, усовершенствовалась на протяжении многих лет. Важно знать, какую библиотеку проверки подлинности используют ваши службы. 

Библиотеки проверки подлинности включают в себя следующее:

* @azure/identity — самый новый пакет проверки подлинности.
* @azure/ms-rest-nodeauth
* @azure/ms-rest-browserauth

Используются старые пакеты проверки подлинности. Если вы используете эти пакеты, рекомендуется перейти со старых методов аутентификации для повышения безопасности и надежности. 

## <a name="best-practices-with-azure-sdk-client-library-authentication"></a>Рекомендации по проверке подлинности клиентской библиотеки Azure SDK

Каждый пакет npm будет отображать параметры проверки подлинности для конкретной клиентской библиотеки. Не следует комбинировать пакеты и коды проверки подлинности, если только все пакеты не используют один и тот же пакет проверки подлинности на странице пакета npm. 

## <a name="azure-identity-library"></a>Библиотека удостоверений Azure

Библиотека удостоверений Azure — это самый новый пакет проверки подлинности для Azure. Ознакомьтесь со списком [поддерживаемых библиотек](https://www.npmjs.com/package/@azure/identity#client-libraries-supporting-authentication-with-azure-identity), использующих удостоверение Azure.

Библиотека [@azure/identity](https://www.npmjs.com/package/@azure/identity) упрощает проверку подлинности в Azure Active Directory для библиотек пакета SDK Azure. Она предоставляет набор реализаций TokenCredential, которые можно передать в библиотеки SDK для проверки подлинности запросов API. Кроме того, она поддерживает проверку подлинности на основе маркеров безопасности с помощью субъекта-службы Azure Active Directory или управляемого удостоверения.

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { BlobServiceClient } = require("@azure/storage-blob");
 
// Enter your storage account name
const account = "<account>";
const defaultAzureCredential = new DefaultAzureCredential();
 
const blobServiceClient = new BlobServiceClient(
  `https://${account}.blob.core.windows.net`,
  defaultAzureCredential
);
```

В предыдущем примере кода JavaScript показано, как использовать библиотеку удостоверений Azure для создания учетных данных Azure по умолчанию, а затем использовать эти учетные данные для доступа к ресурсу службы хранилища Azure.

## <a name="azure-ms-rest--libraries"></a>Библиотеки Azure ms-rest-*
В современных [клиентских библиотеках](../azure-sdk-library-package-index.md#modern-javascripttypescript-libraries) с областью действия `@azure` вам нужен маркер безопасности, чтобы использовать службу. Вы можете получить маркер безопасности, используя метод проверки подлинности клиента Azure SDK, который возвращает учетные данные. 

```javascript
const msRestNodeAuth = require("@azure/ms-rest-nodeauth");
msRestNodeAuth.interactiveLogin().then((credential) => {
    // service code goes here
}).catch((err) => {
    // error code goes here
    console.error(err);
});
```

В предыдущем примере кода JavaScript демонстрируется использование современной библиотеки аутентификации Azure с интерактивным именем входа для получения учетных данных.

```javascript
// service code - this is an example only and not best practices for code flow
const { BlobServiceClient } = require('@azure/storage-blob');
const billingManagementClient = new billing.BillingManagementClient(credential, subscriptionId);
billingManagementClient.enrollmentAccounts.list().then((enrollmentList) => {
    console.log("The result is:");
    console.log(result);
})
```

В предыдущем примере кода JavaScript, показано , как эти учетные данные передаются в конкретную клиентскую библиотеку службы Azure, например в службу хранилища, используемую в следующем примере кода. Клиентская библиотека принимает учетные данные и создает маркер безопасности для вас. Служба использует маркер для аутентификации на уровне службы для запросов. 

Клиентская библиотека управляет маркером безопасности. Ей также известно, когда следует обновлять маркер. Вы, как разработчик с базой кода, не должны управлять этим процессом.

## <a name="older-azure-sdk-client-authentication"></a>Проверка подлинности старой версии клиента пакета SDK Azure 

Более давние клиенты пакета Azure SDK в конечном итоге перейдут на новую версию проверки подлинности, использованную выше. До этого перехода старые версии клиентских библиотек будут использовать разные клиенты проверки подлинности. Они также могут выполнять проверку подлинности с помощью полностью отдельного механизма, такого как ключи ресурсов. 

Для получения наилучших результатов при работе со старыми версиями клиентских библиотек должны быть соблюдены следующие условия: 
* Каждый пакет npm будет отображать параметры проверки подлинности для конкретной клиентской библиотеки.  
* Если текущий код использует современные библиотеки `@azure/ms-*` и старые библиотеки аутентификации в одной базе кода:
    * Убедитесь, что самая старая библиотека с областью действия, отличной от Azure, имеет последнюю версию для вашей службы. Это указано в документации по службе. 
    * Если необходимо продолжить использовать сочетание современных и старых библиотек аутентификации, может потребоваться указать срок действия учетных данных и обновить старую библиотеку, чтобы они соответствовали логике приложения в базе кода. 

## <a name="authentication-with-azure-services-while-developing"></a>Выполнение проверки подлинности с помощью служб Azure во время разработки

Распространенные методы создания необходимых учетных данных во время разработки:

| Тип проверки подлинности Azure|Назначение|
|--|--|
|**Субъект-служба**|Это _рекомендуемый метод_ проверки подлинности. См. сведения о [создании субъекта-службы Azure](node-sdk-azure-authenticate-principal.md). Субъект-служба обеспечивает подключение к Azure отдельно от личной учетной записи Azure. Это может быть временная учетная запись или учетная запись с более длинным сроком службы, которая будет использоваться вместо вашей личной учетной записи.|
| **Интерактивный**| Это самый простой способ проверки подлинности при попытке использования служб Azure. Для этого требуется войти в систему с помощью личной учетной записи в браузере. |
|**Основной**|Для этой проверки подлинности необходимо ввести личное имя пользователя и пароль. Это наименее безопасный метод. Он не рекомендуется.| 

## <a name="authentication-with-azure-services-and-production-code"></a>Проверка подлинности с помощью служб Azure и рабочего кода

Распространенные методы создания необходимых учетных данных в рабочем коде:

|Тип проверки подлинности Azure|Назначение|
|--|--|
|**Управляемые удостоверения службы (MSI)**|[Проверка подлинности на основе MSI](/azure/active-directory/managed-identities-azure-resources/overview) лучше всего подходит для рабочих сценариев. Она не будет использоваться в локальной среде разработки. [Службы](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), поддерживающие MSI.|
|**Сертификаты**|[Сертификаты](/azure/cloud-services/cloud-services-certs-create), которые необходимо отправить в Azure с помощью [портала](/azure/cloud-services/cloud-services-configure-ssl-certificate-portal).|

## <a name="javascript-authentication-samples-for-azure"></a>Примеры проверки подлинности JavaScript для Azure

|Пакет проверки подлинности|Примеры скриптов проверки подлинности|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(рекомендуется)|[Субъект-служба с сертификатом](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[Субъект-служба из файла](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[Интерактивный](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[Основной](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(рекомендуется)|[Проверка подлинности с помощью всплывающего окна (create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[React без всплывающего окна](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[HTML кнопкой входа](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[Субъект-служба](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[Интерактивный](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[Основной](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

## <a name="next-steps"></a>Дальнейшие действия   

* [Пакеты npm Azure](../azure-sdk-library-package-index.md)
* [Документация по пакетам npm Azure](/javascript/api/overview/azure/?view=azure-node-latest)
