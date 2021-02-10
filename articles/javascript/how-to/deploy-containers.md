---
title: Развертывание контейнеров Node.js в Azure
description: Развертывание приложений Node.js в Azure с помощью контейнеров Docker
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 50c26c26fffc0c9377b661d79858dd97318bd3c2
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224788"
---
# <a name="deploy-nodejs-container-to-azure"></a>Развертывание контейнеров Node.js в Azure 

Создание приложения с помощью контейнеров является распространенным шаблоном для обеспечения масштабируемости. Azure предоставляет несколько вариантов развертывания контейнеров.

## <a name="host-your-container-app-on-azure"></a>Размещение приложения контейнера в Azure

Указанные ниже варианты размещения позволяют развертывать контейнерные приложения.

| Служба | Рекомендовано для |
|--|--|
|[Службы приложений](/azure/app-service/quickstart-custom-container?pivots=container-linux)|Развертывание и запуск пользовательского контейнера в службе приложений Azure.|
|[Экземпляры контейнеров](/azure/container-instances/)|Быстрая настройка одного контейнера.|
|[Реестр контейнеров](/azure/container-registry/)|Создание и хранение пользовательских или частных образов контейнеров, а также управление ими.|
|[Служба Kubernetes](/azure/aks/)|Оркестрация нескольких контейнеров.|
|[Виртуальные машины](/azure/virtual-machines) (VM)|Полный контроль над виртуальной машиной Windows или Linux. [Найдите дистрибутив, рекомендованный для Linux](/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json), или [узнайте, как найти](/azure/virtual-machines/linux/cli-ps-findimage) образы виртуальных машин Linux в Azure Marketplace.|

## <a name="build-containerize-and-deploy-app-to-azure"></a>Сборка, контейнеризация и развертывание приложения в Azure

Чтобы начать работу, выберите в списке:
* Руководство по [Express.js](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md)
* Руководство по [Deno](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md)

## <a name="next-steps"></a>Дальнейшие действия

Модули Microsoft Learn:

- [Запуск контейнеров Docker с помощью службы "Экземпляры контейнеров Azure"](/learn/modules/run-docker-with-azure-container-instances/)

- [Создание и хранение образов контейнеров с помощью службы "Реестр контейнеров Azure"](/learn/modules/build-and-store-container-images/)
