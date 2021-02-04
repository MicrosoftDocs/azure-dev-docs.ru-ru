---
title: Библиотеки управления Azure для примеров веб-приложений Java
description: Получите пример кода для создания и обновления веб-приложений Azure, размещенных в службе приложений, используя библиотеки управления Azure для Java.
keywords: Azure, Java, SDK, API, Maven, Gradle, web apps, app service
author: bmitchell287
ms.author: brendm
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 43633e5c-9fb1-4807-ba63-e24c126754e2
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: c273b899a41283b851ce1c95eb2b8f6bc6b0bba5
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528584"
---
# <a name="azure-management-libraries-for-java---web-app-samples"></a>Библиотеки управления Azure для Java — примеры веб-приложений 

В следующей таблице представлены ссылки на исходный код Java, который можно использовать для создания и настройки веб-приложений.

| Образец | Описание |
|---|---|
| **Создание приложения** ||
| [Создание веб-приложения и развертывание из FTP или GitHub][1] | Развертывание веб-приложений из локального репозитория Git или клиента FTP и непрерывная интеграция с репозиторием GitHub. |
| [Создание веб-приложения и управление слотами развертывания][2] | Создание веб-приложения и его развертывание в промежуточных слотах с последующим переключением между промежуточным и рабочим слотами. |
| **Настройка приложения** ||
| [Создание веб-приложения и настройка личного домена][3] | Создание веб-приложения с личным доменом и самозаверяющим SSL-сертификатом. |
| **Масштабирование приложения** ||
| [Масштабирование веб-приложения с высоким уровнем доступности в нескольких регионах][4] | Масштабирование веб-приложений в трех разных географических регионах и предоставление к ним доступа через одну конечную точку с помощью диспетчера трафика Azure. | 
| **Подключение приложения к ресурсам** ||
| [Подключение веб-приложения к учетной записи хранения][5] | Создание учетной записи хранения Azure и добавление строки подключения учетной записи хранения к параметрам приложения. |
| [Подключение веб-приложения к базе данных SQL][6] | Создание веб-приложения Azure и базы данных SQL, а также добавление строки подключения базы данных SQL к параметрам приложения. |

[1]: ./index.yml
[2]: https://github.com/Azure-Samples/app-service-java-manage-staging-and-production-slots-for-web-apps/
[3]: https://github.com/Azure-Samples/app-service-java-manage-web-apps-with-custom-domains/
[4]: https://github.com/Azure-Samples/app-service-java-scale-web-apps-on-linux
[5]: https://github.com/Azure-Samples/app-service-java-manage-storage-connections-for-web-apps/
[6]: https://github.com/Azure-Samples/app-service-java-manage-data-connections-for-web-apps/
