---
title: Развертывание контейнеров Node.js в Azure
description: Развертывание приложений Node.js в Azure с помощью контейнеров Docker
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 6bf8acb66a708433966bdfe90cc358a56d2d3b42
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857812"
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

Чтобы приступить к работе, ознакомьтесь со сведениями из [этого учебника](develop-nodejs-on-azure.md), чтобы узнать, как выполнять следующие действия:

* Скачивание образца кода
* Запуск приложения Node.js
* Отладка приложения в Visual Studio Code.
* Контейнеризация приложения Node.js MEAN.
* Развертывание приложения с помощью команд Azure CLI.
* Создание сервера MongoDB в ресурсе CosmosDB.
* Добавление образа контейнера в частный реестр контейнеров.
* Добавление личного доменного имени в веб-приложение.
* Масштабирование веб-приложения до большего размера.
* Создание и удаление группы ресурсов для всех ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

Модули Microsoft Learn:

- [Запуск контейнеров Docker с помощью службы "Экземпляры контейнеров Azure"](/learn/modules/run-docker-with-azure-container-instances/)

- [Создание и хранение образов контейнеров с помощью службы "Реестр контейнеров Azure"](/learn/modules/build-and-store-container-images/)
