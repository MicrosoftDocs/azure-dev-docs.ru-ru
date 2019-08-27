---
title: Развертывание веб-приложений Node.js в Azure
description: Начало работы со службой приложений Azure и другие варианты размещения для веб-приложений, включая прогрессивные веб-приложения (PWA)
author: kraigb
manager: barbkess
ms.devlang: nodejs
ms.topic: article
ms.service: azure-nodejs
ms.date: 08/20/2019
ms.author: kraigb
ms.openlocfilehash: c58cd1ed3480fae88d9839b44f0f4062c825a068
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892348"
---
# <a name="how-to-deploy-web-apps-to-azure"></a>Развертывание веб-приложений в Azure

В Azure существует несколько вариантов размещения веб-приложений:

- Лучшим вариантом размещения для веб-приложений является Служба приложений Azure, предложение "платформа как услуга" (PaaS). Для начала работы воспользуйтесь одним из следующих ресурсов:

  - [Создание веб-приложений Node.js в Azure](/azure/app-service/app-service-web-get-started-nodejs)
  - [Try Azure App Service — Create an Express app from a template](https://code.visualstudio.com/tryappservice/?utm_source=msftdocs&utm_medium=microsoft&utm_campaign=tryappservice) (Пробная версия службы приложений Azure. Создание приложения Express на основе шаблона)
  - [Размещение веб-приложения с помощью Службы приложений Azure. Модуль "Изучение"](/learn/modules/host-a-web-app-with-azure-app-service/index)
  - [Разработка приложения на основе Node.js и MongoDB в Azure](/azure/app-service/app-service-web-tutorial-nodejs-mongodb-app)
  - [Примеры Службы приложений](/samples/browse/?languages=javascript%2Cnodejs&products=azure-app-service)

- Вы можете создавать собственные контейнеры и развертывать их в Azure с помощью Реестра контейнеров Azure и службы Azure Kubernetes. Дополнительные сведения см. в статье [Как развертывать контейнеры Node.js в Azure](node-howto-deploy-containers.md).

- Если вы предпочитаете работать в основном с бессерверным кодом, ознакомьтесь со сведениями о том, [Как записывать бессерверный код Node.js в Azure](node-howto-write-serverless-code.md).

- Дополнительные сведения о создании сайта JAMstack (статического) см. в статье [Как создавать веб-приложения JAMstack (статический сайт) с помощью Azure](node-howto-create-static-site-jamstack.md).

- Если вы хотите управлять инфраструктурой, можно просто использовать виртуальную машину. Чтобы приступить к работе, следуйте указаниям в статье [Развертывание веб-сайта с виртуальными машинами Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) на Microsoft Learn.

Полный обзор различных вариантов размещения см. в разделе [Дерево принятия решений для вычислительных служб Azure](/azure/architecture/guide/technology-choices/compute-decision-tree), а также модуль [Основные облачные службы — варианты вычислений Azure](/learn/modules/intro-to-azure-compute/) на Microsoft Learn.
