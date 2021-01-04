---
title: Код на React с использованием Компьютерного зрения
description: Этот пример содержит весь код, необходимый для подключения Компьютерного зрения к приложению React. В этом разделе руководства _рассматривается_ процесс и код для такого подключения.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 44999c92ef2a938327a379d0d4993b000466fb3f
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394068"
---
# <a name="5-review-how-to-add-computer-vision-to-the-react-app"></a>5. Обзорные сведения о том, как добавить Компьютерное зрение в приложение React

Этот пример содержит весь код TypeScript, необходимый для подключения Компьютерного зрения к приложению React. В этом разделе руководства _рассматривается_ процесс и код для такого подключения. Для работы с этим руководством вам не придется выполнять этот процесс. 

## <a name="add-computer-vision-to-local-react-app"></a>Добавление Компьютерного зрения в локальное приложение React

С помощью npm добавьте Компьютерное зрение в файл package.json. 

```bash
npm install @azure/cognitiveservices-computervision 
```

## <a name="add-computer-vision-code-as-separate-module"></a>Добавление кода Компьютерного зрения в отдельный модуль

Код Компьютерного зрения размещается в отдельном файле с именем `./src/VisualAI.js`. Здесь выделена функция main этого модуля. 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/VisualAI.js" highlight="55-75" :::

## <a name="add-catalog-of-images-as-separate-module"></a>Добавление каталога изображений в отдельный модуль

Это приложение выбирает случайное изображение из каталога, если пользователь не ввел URL-адрес изображения. Здесь выделена функция случайного выбора 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/DefaultImages.js" highlight="33-35" :::

## <a name="add-custom-computer-vision-module-to-react-app"></a>Добавление пользовательского модуля Компьютерного зрения в приложение React

Добавьте методы в файл React `app.js`. Здесь выделены фрагменты анализа изображения и отображения результатов.

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/App.js" highlight="20-25, 29-42" :::

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Очистка ресурсов](clean-up-resources.md) 