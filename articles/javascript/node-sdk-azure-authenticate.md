---
title: Аутентификация с использованием модулей управления Azure для Node.js
description: Аутентификация с помощью субъекта-службы в модулях управления Azure для Node.js
ms.topic: article
ms.date: 06/17/2017
ms.custom: devx-track-javascript
ms.openlocfilehash: 1d3f0d2930d397c24177f0cee7a9e276c4df9d67
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110426"
---
# <a name="authenticate-with-the-azure-modules-for-nodejs"></a>Аутентификация с использованием модулей Azure для Node.js

Все API-интерфейсы служб, для которых создаются экземпляры, должны пройти аутентификацию с использованием объекта `credentials`. Существует три способа аутентификации и создания необходимых учетных данных с использованием пакета Azure SDK для Node.js:

- обычная проверка подлинности
- Интерактивный вход
- Проверка подлинности субъекта-службы

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="basic-authentication"></a>обычная проверка подлинности

Чтобы выполнить аутентификацию программными средствами с помощью учетных данных учетной записи Azure, используйте функцию `loginWithUsernamePassword`. В следующем фрагменте кода JavaScript показано, как выполнять обычную аутентификацию с использованием учетных данных, которые хранятся как переменные среды.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.loginWithUsernamePassword(process.env.AZURE_USER,
                                 process.env.AZURE_PASS,
                                 (err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials,
                                                             '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="interactive-login"></a>Интерактивный вход

При интерактивном входе предоставляются ссылки и код, что позволяет пользователю выполнять аутентификацию в браузере. Применяйте этот метод, если в одном скрипте используется несколько учетных записей или если требуется вмешательство пользователя.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.interactiveLogin((err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials, '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

[Интерактивный вход](#interactive-login) — самый простой способ аутентификации. Но при использовании пакета SDK для Node.js может потребоваться использовать обычную аутентификацию субъекта-службы, а не предоставлять учетные данные учетной записи. В руководстве по [созданию субъекта-службы Azure с помощью Node.js](./node-sdk-azure-authenticate-principal.md) описаны разные способы создания и использования субъекта-службы.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание статического веб-сайта в Azure из Visual Studio Code](tutorial-vscode-static-website-node-01.md)