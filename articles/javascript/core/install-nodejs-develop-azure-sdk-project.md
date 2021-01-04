---
title: Установка Node.js для разработки приложений с использованием Azure SDK
description: Узнайте, как установить Node.js для распространенных сценариев разработки с использованием Azure.
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5fdaca001f82afca942a22ec9ad971f4af18c8b8
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394043"
---
# <a name="install-and-manage-nodejs-for-common-azure-development-scenarios"></a>Установка и администрирование Node.js для распространенных сценариев разработки в Azure

При установке Node.js для разработки в Azure необходимо учитывать как локальную среду разработки, так и среду размещения для развертывания. Azure предоставляет размещение для Node.js в Windows и Linux (в версии долгосрочной поддержки (LTS)). 

## <a name="minimum-version-of-nodejs-8"></a>Минимальная версия — Node.js 8

Минимальная версия, которую поддерживает Azure SDK, — Node.js 8. 

## <a name="download-and-install-nodejs-based-on-your-intended-use"></a>Скачивание и установка версии Node.js в соответствии с ее предполагаемым назначением

Вы можете скачать и установить версию Node.js, соответствующую вашим требованиям.
 
* [Страница скачивания Node.js](https://nodejs.org/en/download/) 
* [Официальный образ Docker](https://hub.docker.com/_/node/)

## <a name="managing-versions-of-nodejs"></a>Управление версиями Node.js

Если вам нужно управлять несколькими версиями Node.js для разработки в Azure, используйте nvm:

* OSX, *nix — [nvm](https://github.com/creationix/nvm);
* Windows — [nvm для Windows](https://github.com/marcelklehr/nodist).

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка локальной среды разработки](configure-local-development-environment.md) для использования Azure SDK
