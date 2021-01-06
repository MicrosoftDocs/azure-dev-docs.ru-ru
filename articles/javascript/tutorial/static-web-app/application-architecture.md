---
title: Архитектура приложения Компьютерного зрения
description: В этом разделе учебника описывается клиентское приложение и процесс развертывания.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 12d156591393607f4cb40094fa00dda1a0f947fd
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97690808"
---
# <a name="2-application-architecture-for-static-web-app-with-computer-vision"></a>2. Архитектура приложения для статического веб-приложения с Компьютерным зрением

Сведения о клиентском приложении и процессе развертывания.

## <a name="what-is-an-azure-static-web-app"></a>Что такое статическое веб-приложение Azure?

При создании статических веб-приложений вы можете настроить в Azure несколько разных параметров в зависимости от требуемой функциональности и степени контроля. В этом руководстве рассматривается самый простой вариант службы, в котором большинство параметров уже выбраны за вас, чтобы вы могли сосредоточиться на коде интерфейса, не отвлекаясь на среду размещения.

## <a name="client-application-architecture"></a>Архитектура клиентского приложения

Приложение React (create-react-app) реализует следующие возможности: 
* отображение сообщения, если ключ и конечная точка Azure для [**Компьютерного зрения**](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) Cognitive Services не найдены;
* анализ изображения с помощью Компьютерного зрения Cognitive Services:
    * получение изображения для анализа по общедоступному URL-адресу или из коллекции;
    * по завершении анализа:
        * Отображение изображения
        * отображение результатов от Компьютерного зрения в формате JSON. 

:::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="Снимок части экрана: браузер с примером приложения React для Компьютерного зрения Cognitive Services при возврате результата анализа.":::

## <a name="deploy-to-azure-with-github-action"></a>Развертывание в Azure с помощью действия GitHub

При отправке данных в определенную ветвь действие GitHub выполняет следующие задачи:
* встраивает в сборку секреты GitHub, содержащие ключ и конечную точку для Компьютерного зрения;
* компилирует клиент React (create-react-app);
* перемещает полученные файлы в ресурс [**статического веб-приложения**](https://docs.microsoft.com/azure/static-web-apps) Azure.

> [!div class="nextstepaction"]
> [Локальное скачивание и запуск приложения React для анализа изображений в Cognitive Services](run-the-react-cognitive-services-image-analyzer-app-locally.md) 