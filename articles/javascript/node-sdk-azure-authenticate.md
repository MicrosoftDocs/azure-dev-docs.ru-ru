---
title: Аутентификация с использованием модулей управления Azure для Node.js
description: Аутентификация с помощью субъекта-службы в модулях управления Azure для Node.js
ms.topic: how-to
ms.date: 06/17/2017
ms.custom: devx-track-js
ms.openlocfilehash: 150b00c4dbb21d0514d1d7c7d34813272bbf06e1
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586125"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Проверка подлинности с использованием модулей управления Azure для JavaScript

Существует два набора пакетов управления для служб Azure, которые используются для управления ресурсами.
- Пакет Azure SDK для Node.js
- Пакет Azure SDK для JavaScript

Пакет Azure SDK для Node.js — это старый набор пакетов управления для служб Azure, которые: 
- можно использовать только в Node.js, а не в браузерах;
- написаны на языке JavaScript с файлами объявлений типа, написанными вручную;
- не находятся в активной разработке и являются устаревшими, вместо которых используется пакет Azure SDK для пакетов JavaScript;
- содержат имена пакетов, которые начинаются с `azure-arm-`;
- требуют пакет [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure), чтобы создать учетные данные, которые затем можно передать в классы клиента в пакетах для проверки подлинности с помощью Azure Active Directory;
- существуют в репозитории https://github.com/Azure/azure-sdk-for-node.

Пакет Azure SDK для JavaScript — это более новый набор пакетов управления для служб Azure, которые:
- можно использовать в Node.js и в браузерах;
- написаны на языке TypeScript и могут использоваться в проектах JavaScript и TypeScript;
- находятся в активной разработке и получают обновления по мере того, как службы Azure обновляют API управления ресурсами;
- содержат имена пакетов, которые начинаются с `@azure/arm-`;
- требуют пакет [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth), чтобы создать учетные данные, которые затем можно передать в классы клиента в пакетах для проверки подлинности с помощью Azure Active Directory (если приложение выполняется в браузере, вместо приведенного выше пакета используйте [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth));
- существуют в репозитории https://github.com/Azure/azure-sdk-for-js.

Простой способ различить эти два набора пакетов — взглянуть на их имена.

Все API-интерфейсы служб, для которых создаются экземпляры, должны пройти аутентификацию с использованием объекта `credentials`. Существует несколько способов выполнить проверку подлинности и создать необходимые учетные данные для пакетов Azure SDK для Node.js и Azure SDK для JavaScript.

Ниже перечислены некоторые из них.

- Обычная проверка подлинности с использованием имени пользователя и пароля
- Интерактивный вход, который является самым простым способом проверки подлинности, но для него требуется войти с помощью учетной записи пользователя.
- Проверка подлинности на основе субъекта-службы В руководстве по [созданию субъекта-службы Azure с помощью Node.js](./node-sdk-azure-authenticate-principal.md) описаны разные способы создания субъекта-службы. 

В файле сведений для каждого приведенного ниже пакета содержатся данные о различных способах получения объекта учетных данных.
- [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) при использовании любого пакета управления в Azure SDK для JavaScript в Node.js.
- [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth) при использовании любого пакета управления в Azure SDK для JavaScript в браузере.
- [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) при использовании любого пакета управления в старом пакете Azure SDK для Node.js.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="next-steps"></a>Дальнейшие действия   

* [Развертывание статического веб-сайта в Azure из Visual Studio Code](tutorial-vscode-static-website-node-01.md)