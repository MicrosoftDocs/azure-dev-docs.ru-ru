---
title: Клонирование репозитория GitHub с помощью VSCode
description: Клонирование общедоступного репозитория из GitHub на локальный компьютер, используя Visual Studio Code.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: f3bf194f7520779b92d4cfb5966eedb0f599edea
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224808"
---
# <a name="clone-and-use-a-github-repository-in-visual-studio-code"></a>Клонирование и использование репозитория GitHub в Visual Studio Code

Сведения о клонировании общедоступного репозитория из GitHub на локальный компьютер с помощью Visual Studio Code.

При работе в Visual Studio Code с репозиторием необходимо использовать отдельные средства:

|Значок|Сведения|[Доступ из](https://code.visualstudio.com/docs/getstarted/userinterface)|
|--|--|--|
|| [Git CLI](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)|палитра команд — F1|
|:::image type="content" source="../../media/how-to-clone-github-repo/git-commit-icon-activity-bar.png" alt-text="Значок системы управления версиями":::|Расширение системы управления версиями|Панель действий|
|:::image type="content" source="../../media/how-to-clone-github-repo/github-icon-activity-bar.png" alt-text="Значок для Prs и Issues в GitHub":::|Расширение GitHub|Панель действий|

В следующих процедурах будут использованы именованные части[пользовательского интерфейса Visual Studio Code](https://code.visualstudio.com/docs/getstarted/userinterface). 

## <a name="use-command-palette-to-clone-repository"></a>Использование палитры команд для клонирования репозитория

Чтобы приступить к работе, скачайте пример проекта, как описано ниже.

1. Нажмите клавишу **F1**, чтобы отобразить палитру команд.

1. В строке палитры команд введите `gitcl`, выберите команду **Git: клонировать** и нажмите клавишу **ВВОД**.

    ![Команда gitcl в строке командной палитры Visual Studio Code](../../media/how-to-clone-github-repo/visual-studio-code-git-clone.png)

1. Если появится запрос, введите **URL-адрес репозитория GitHub**, а затем нажмите клавишу **ВВОД**.

1. Выберите (или создайте) локальный каталог, в который нужно клонировать проект.

    ![Обозреватель Visual Studio Code](../../media/how-to-clone-github-repo/visual-studio-code-explorer.png)

## <a name="create-a-branch-for-changes-with-git-cl"></a>Создание ветви для изменений с помощью Git CLI

Используйте Git в палитре команд, чтобы создать ветвь.

1. Нажмите клавишу **F1**, чтобы отобразить палитру команд.
1. Найдите элемент `git branch` и выберите `Git: Create Branch`.

    :::image type="content" source="../../media/how-to-clone-github-repo/git-cli-branch-list.png" alt-text="Экран &quot;Поиск по фразе &quot;git branch&quot; и выбор пункта &quot;Git: Create Branch&quot;.":::

1. Введите имя для новой ветви. Имя ветви будет отображатся в строке состояния. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="Экран &quot;Отображение имени ветви в строке состояния&quot;.":::

## <a name="create-a-branch-from-status-bar"></a>Создание ветви из строки состояния

1. Выберите имя ветви в строке состояния. 

    Строка состояния обычно находится в нижней части Visual Studio code. 

1. Выберите в палитре команд **Создание новой ветви**.
1. Введите имя для новой ветви. 

1. Введите имя для новой ветви. Имя ветви будет отображатся в строке состояния. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="Экран &quot;Отображение имени ветви в строке состояния&quot;.":::

## <a name="commit-changes-with-git"></a>Фиксация изменений с помощью Git 

Изменив ветвь, зафиксируйте изменения.

1. Перейдите на панель действий и выберите значок Git.

1. В поле **Сообщение** введите сообщение фиксации и нажмите клавиши **Ctrl**+**ВВОД** или установите флажок на панели управления версиями.

    ![Добавление файла yarn.lock в Git](../../media/how-to-clone-github-repo/visual-studio-code-add-yarn-lock.png)

## <a name="push-a-local-branch-to-remote-from-status-bar"></a>Отправка локальной ветви в удаленное расположение из строки состояния

1. Щелкните значок "Отправить" в правой части окна имени ветви. 
1. Выберите имя удаленного расположения во всплывающем окне. Если вы имеете только одно удаленное расположение, вам не будет предложено выбрать имя такого расположения. 

## <a name="push-a-local-branch-to-remote-from-the-source-control-extension"></a>Отправка локальной ветви в удаленное расположение из расширения "Система управления версиями"
1. Щелкните значок "Система управления версиями" на панели действий. 
1. Нажмите кнопку с многоточием (...), выберите **Pull, Push** (Получение, отправка) а затем — **Отправить в**. 
1. Выберите имя удаленного расположения во всплывающем окне. Если вы имеете только одно удаленное расположение, вам не будет предложено выбрать имя такого расположения. 

## <a name="next-steps"></a>Следующие шаги

* Узнайте о [развертывании веб-приложения](../deploy-web-app.md)
