---
title: Создание ресурса статического веб-приложения
description: Создайте для этой службы ресурс статического веб-приложения с расширением Visual Studio Code.
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c6daa3f2a7ceb7f981cdaf57bdba61a722edbaa3
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993586"
---
# <a name="4-create-azure-static-web-app-resource"></a>4. Создание ресурса статического веб-приложения Azure

В этом разделе руководства вы узнаете, как создать для этой службы ресурс статического веб-приложения с расширением Visual Studio Code и отправить локальный код в удаленный репозиторий, где он будет скомпилирован в приложение для развертывания в Azure.

## <a name="create-a-new-branch-dedicated-to-deployment"></a>Создание новой ветви для развертывания

Статическое веб-приложение Azure получает данные для сборки из определенной ветви репозитория GitHub. До текущего момента в рамках этого руководства использовалась ветвь `main`. В новом терминале Visual Studio Code создайте ветвь `live`, которая потребуется только для сборки и развертывания приложения.

```bash
git checkout -b live
```

## <a name="push-the-live-branch-to-github"></a>Отправка динамической ветви в GitHub

В терминале Visual Studio Code отправьте локальную ветвь `live` в удаленный репозиторий.

```bash
git push origin live
```

## <a name="create-a-static-web-app-resource"></a>Создание ресурса статического веб-приложения

1. Выберите значок **Azure**, щелкните правой кнопкой мыши службу **Статические веб-приложения** и выберите элемент **Создать статическое веб-приложение...** 

    :::image type="content" source="../../media/static-web-app/visualstudiocode-storage-extension-create-static-web-resource.png" alt-text="Снимок экрана: Visual Studio Code с расширением Visual Studio":::

1. Поочередно введите значения в следующие поля (по одному за раз): 

    |Имя поля| value|
    |--|--|
    |Имя статического веб-приложения.|`Demo-ComputerVisionAnalyzer`|
    |Выбор ветви для репозитория|`live`| 
    |Выберите расположение для кода приложения.|`/`|
    |Выберите расположение для кода Функций Azure.|Выберите **Пока пропустить**|
    |Введите путь к выходным данным сборки относительно расположения приложения.|`build`|
    |Выбор расположения для новых ресурсов|Выберите ближайшее к вам расположение Azure.|

## <a name="update-the-github-action-with-secret-environment-variables"></a>Обновление переменных среды с секретами в действии GitHub

Ключ и конечная точка Компьютерного зрения находятся в коллекции секретов репозитория, но пока не переданы в действие GitHub. На этом шаге к действию добавляются ключ и конечная точка.

1. Получите изменения, внесенные при создании ресурса Azure, чтобы получить файл действий GitHub.

    ```bash
    git pull origin live
    ```

1. В редакторе Visual Studio Code измените файл действий GitHub, расположенный в `./.github/workflows/`, добавив в него секреты. 

    :::code language="yml" source="~/../js-e2e-client-cognitive-services/.github/workflows/sample-github-workflow.yml" highlight="34-36" :::

    
1. Добавьте и зафиксируйте изменения в локальной ветви `live`.

    ```bash
    git add . && git commit -m "add secrets to action"
    ```

1. Отправьте изменения в удаленный репозиторий. Будет активировано новое действие сборки и развертывания в статическое веб-приложение Azure.

    ```bash
    git push origin live
    ```

## <a name="view-the-github-action-build-process"></a>Просмотр процесса сборки действий GitHub

1. В веб-браузере откройте репозиторий GitHub для этого руководства и выберите **Actions** (Действия). 

1. Выберите верхнюю сборку в списке, затем щелкните элемент **Build and Deploy Job** (Задание сборки и развертывания), чтобы просмотреть сведения о процессе сборки. Дождитесь, пока процесс **Build And Deploy** (Сборка и развертывание) успешно завершится.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-action-build-react-computer-vision-app.png" alt-text=" Выберите верхнюю сборку в списке, затем щелкните элемент Build and Deploy Job (Задание сборки и развертывания), чтобы просмотреть сведения о процессе сборки. Дождитесь, пока сборка успешно завершится.":::

## <a name="view-azure-static-web-site-in-browser"></a>Просмотр статического веб-сайта Azure в браузере

1. В Visual Studio Code выберите значок **Azure** в крайнем меню справа. Затем выберите созданное статическое веб-приложение, щелкните правой кнопкой мыши элемент **Browse site** (Обзор сайта) и выберите команду **Open** (Открыть), чтобы просмотреть общедоступный статический веб-сайт. 

:::image type="content" source="../../media/static-web-app/visualstudiocode-browse-static-web-app.png" alt-text="Выберите элемент Browse site (Обзор сайта), а затем — команду Open (Открыть), чтобы просмотреть общедоступный статический веб-сайт. ":::

Кроме того, URL-адрес этого сайта можно также найти:
* на странице **Обзор** для ресурса на портале Azure;
* в выходных данных сборки и развертывания из действия GitHub, где URL-адрес сайта выводится в самом конце скрипта. 

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Проверка кода React и анализ с помощью Компьютерного зрения Cognitive Services](add-computer-vision-react-app.md)