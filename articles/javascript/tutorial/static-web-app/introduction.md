---
title: Введение и предварительные требования
description: Локальное создание и развертывание клиентского приложения React в виде статического веб-приложения Azure с помощью действия GitHub.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5d70e14a9a5247f99c8b6e033af0a225b27da30b
ms.sourcegitcommit: 291768a67862336267c67819e913c16710e3875e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95820682"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Создание и развертывание статического веб-приложения в Azure

В этом руководстве описано локальное создание и развертывание клиентского приложения React в виде статического веб-приложения Azure с помощью действия GitHub. 

Приложение React (create-react-app) реализует следующие возможности: 
* отображение сообщений о том, что для Компьютерного зрения Cognitive Services не найдены ключ или конечная точка Azure;
* анализ изображения с помощью Компьютерного зрения Cognitive Services:
    * получение изображения для анализа по общедоступному URL-адресу или из коллекции;
    * по завершении анализа:
        * Отображение изображения
        * отображение результатов от Компьютерного зрения в формате JSON. 

При отправке данных в определенную ветвь действие GitHub выполняет следующие задачи:
* встраивает в сборку секреты GitHub, содержащие ключ и конечную точку для Компьютерного зрения;
* компилирует клиент React (create-react-app);
* перемещает полученные файлы в ресурс статического веб-приложения Azure.

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="what-is-an-azure-static-web-app"></a>Что такое статическое веб-приложение Azure?

При создании статических веб-приложений вы можете настроить в Azure несколько разных параметров в зависимости от требуемой функциональности и степени контроля. В этом руководстве рассматривается самый простой вариант службы, в котором большинство параметров уже выбраны за вас, чтобы вы могли сосредоточиться на коде интерфейса, не отвлекаясь на среду размещения.

## <a name="prerequisites"></a>Предварительные требования

- [Установите Azure CLI](/cli/azure/install-azure-cli) или используйте [Azure Cloud Shell](https://shell.azure.com.).
- Установите на локальный компьютер [Node.js и npm](https://nodejs.org/en/download).
- Установите на локальный компьютер [Visual Studio Code](https://code.visualstudio.com/). 
    - [Статические веб-приложения Azure (предварительная версия)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) для развертывания приложения React в статическое веб-приложение Azure.
- Средство [Git](https://git-scm.com/downloads) для отправки данных в GitHub, по которым активируется действие GitHub.
- [Учетная запись GitHub](https://github.com/join) для создания вилки репозитория и отправки данных.

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Локальное скачивание и запуск приложения React для анализа изображений в Cognitive Services](run-the-react-cognitive-services-image-analyzer-app-locally.md) 