---
title: Аутентификация с использованием модулей управления Azure для Node.js
description: Аутентификация с помощью субъекта-службы в модулях управления Azure для Node.js
ms.topic: how-to
ms.date: 09/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: 084a7154bf52314886c0dfb57db3e9c879c8e2c0
ms.sourcegitcommit: c3a1c9051b89870f6bfdb3176463564963b97ba4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437253"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Проверка подлинности с использованием модулей управления Azure для JavaScript

Все клиентские библиотеки [SDK](../azure-sdk-library-package-index.md) требуют выполнить проверку подлинности через объект `credentials` при создании экземпляра. Существует несколько способов выполнить проверку подлинности и создать требуемые учетные данные.

Распространенные методы создания требуемых учетных данных:

- **Проверка подлинности** с помощью субъекта-службы — это _рекомендуемый метод_ . См. сведения о [создании субъекта-службы Azure](node-sdk-azure-authenticate-principal.md). 
- **Интерактивный вход** , который является самым простым способом проверки подлинности, но для него требуется войти с помощью учетной записи пользователя и браузера.
- **Обычная** проверка подлинности (с использованием имени пользователя и пароля). Это наименее безопасный метод. 

## <a name="samples"></a>Примеры

|Пакет проверки подлинности|Примеры скриптов проверки подлинности|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(рекомендуется)|[Субъект-служба с сертификатом](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[Субъект-служба из файла](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[Интерактивный](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[Основной](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(рекомендуется)|[Проверка подлинности с помощью всплывающего окна (create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[React без всплывающего окна](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[HTML кнопкой входа](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[Субъект-служба](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[Интерактивный](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[Основной](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

[!INCLUDE [chrome-note](../includes/chrome-note.md)]

## <a name="next-steps"></a>Дальнейшие действия   

* [Развертывание статического веб-сайта в Azure из Visual Studio Code](../tutorial-vscode-static-website-node-01.md)