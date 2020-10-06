---
ms.custom: devx-track-js
ms.topic: include
ms.date: 09/30/2020
ms.openlocfilehash: 36265a01dc58f4c21d23feea900ea53fba8c8846
ms.sourcegitcommit: 0b1c751c5a4a837977fec1c777bca5ad15cf2fc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91621688"
---
## <a name="types-of-sdk-client-libraries"></a>Типы клиентских библиотек SDK

Существует два набора пакетов управления для служб Azure, которые используются для управления ресурсами.

|Функция|Пакет Azure SDK для JavaScript<br>(рекомендуется, современный)|Пакет Azure SDK для Node.js<br>(устаревшая версия)|
|--|--|--|
|В активной разработке|✔️|❌|
|Используется в Node.js|✔️|✔️|
|Используется в браузере|✔️|❌|
|Используется в JavaScript|✔️|✔️|
|Используется в TypeScript<br>(включает .d.ts)|✔️|❌|
|Библиотека проверки подлинности<br>[примеры](../node-sdk-azure-authenticate.md)|Не в браузере: [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth)<br>В браузере: [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)|Не в браузере: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|
|Репозиторий GitHub|[Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js)|[Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node)|
