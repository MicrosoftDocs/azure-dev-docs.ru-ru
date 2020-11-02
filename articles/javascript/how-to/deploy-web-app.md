---
title: Развертывание веб-приложений Node.js в Azure
description: Начало работы со службой приложений Azure и другие варианты размещения для веб-приложений, включая прогрессивные веб-приложения (PWA)
ms.topic: how-to
ms.date: 08/20/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: ee2d9a58baa306309df28f1a4bf6515481fccf3e
ms.sourcegitcommit: c3a1c9051b89870f6bfdb3176463564963b97ba4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437274"
---
# <a name="deploy-nodejs-web-apps-to-azure-app-service"></a>Развертывание веб-приложений Node.js в Службе приложений Azure

В Azure существует несколько вариантов развертывания и размещения веб-приложений:

- Лучшим вариантом размещения для веб-приложений является Служба приложений Azure, предложение "платформа как услуга" (PaaS). Для начала работы воспользуйтесь одним из следующих ресурсов:

  - [Создание веб-приложений Node.js в Azure](/azure/app-service/app-service-web-get-started-nodejs)
  - [Try Azure App Service — Create an Express app from a template](https://code.visualstudio.com/tryappservice/?utm_source=msftdocs&utm_medium=microsoft&utm_campaign=tryappservice) (Пробная версия службы приложений Azure. Создание приложения Express на основе шаблона)
  - [Размещение веб-приложения с помощью Службы приложений Azure. Модуль "Изучение"](/learn/modules/host-a-web-app-with-azure-app-service/index)
  - [Разработка приложения на основе Node.js и MongoDB в Azure](/azure/app-service/app-service-web-tutorial-nodejs-mongodb-app)
  - [Примеры Службы приложений](/samples/browse/?languages=javascript%2Cnodejs&products=azure-app-service)

- Вы можете создавать собственные контейнеры и развертывать их в Azure с помощью Реестра контейнеров Azure и службы Azure Kubernetes. Дополнительные сведения см. в статье [Как развертывать контейнеры Node.js в Azure](deploy-containers.md).

- Если вы предпочитаете работать в основном с бессерверным кодом, ознакомьтесь со сведениями о том, [Как записывать бессерверный код Node.js в Azure](develop-serverless-apps.md).

- Дополнительные сведения о создании сайта JAMstack (статического) см. в статье [Как создавать веб-приложения JAMstack (статический сайт) с помощью Azure](create-static-site.md).

- Если вы хотите управлять инфраструктурой, можно просто использовать виртуальную машину. Чтобы приступить к работе, следуйте указаниям в статье [Развертывание веб-сайта с виртуальными машинами Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) на Microsoft Learn.

Полный обзор различных вариантов размещения см. в разделе [Дерево принятия решений для вычислительных служб Azure](/azure/architecture/guide/technology-choices/compute-decision-tree), а также модуль [Основные облачные службы — варианты вычислений Azure](/learn/modules/intro-to-azure-compute/) на Microsoft Learn.