---
title: include file tutorial-azure-web-app-mongodb-01.md
description: include file tutorial-azure-web-app-mongodb-01.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: a8c9fcca1b7374ae5122cfabb3fcbfc6ba8cdd07
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183910"
---
В этом разделе руководства вам потребуется подписка Azure и все программное обеспечение для работы с этим руководством.

## <a name="create-or-use-existing-azure-subscription"></a>Создание новой или использование существующей подписки Azure 

* Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension).

## <a name="install-software"></a>Установка ПО

- Установите на локальный компьютер [Node.js и npm](https://nodejs.org/en/download) (диспетчер пакетов Node.js).
- [Docker.](https://docs.docker.com/get-docker/) Docker используется для предоставления локальной базы данных MongoDB без установки MongoDB. 
    - Если для получения локальной базы данных MongoDB необходимо использовать Docker, вам также потребуются указанные ниже компоненты.
        -  [Контейнеры разработки](https://code.visualstudio.com/docs/remote/containers) Visual Studio предоставляют несколько общих контейнеров для разработки JavaScript. 
        - [Удаленные контейнеры](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
    - Если у вас уже есть локальная база данных MongoDB и вы не хотите устанавливать Docker, вы все равно можете выполнить этот шаг. Во всех шагах, где для доступа к работающей локально базе данных MongoDB используется контейнер разработки, можно использовать собственную локальную базу данных MongoDB, если доступен следующий URL-адрес MongoDB: 
        - `mongodb://localhost:27017`
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
- Расширения Visual Studio Code:
    - [Расширение Службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для Visual Studio Code (устанавливается из Visual Studio Code).
    - [Базы данных Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="want-to-know-more"></a>Хотите получить дополнительные сведения? 

Дополнительные расширения Visual Studio Code:
* [MongoDB для VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)
* [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)