---
title: Создание веб-приложения Hello World для Службы приложений Azure с помощью Eclipse
description: В этом учебнике показано, как с помощью набора средств Azure для Eclipse создать веб-приложение Hello World для Azure.
services: app-service
keywords: java, eclipse, web app, azure app service, hello world, quick start
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 25c46576e6c1c379d10cd5ce350ca73c5e28ab93
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81671020"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Создание веб-приложения Hello World для Службы приложений Azure с помощью Eclipse

С помощью модуля с открытым кодом [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) всего за несколько минут можно создать и развернуть в Службе приложений Azure базовое приложение Hello World в качестве веб-приложения.

> [!NOTE]
>
> Если вы предпочитаете использовать IntelliJ IDEA, ознакомьтесь с нашим [руководством по IntelliJ][intellij-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Обязательно очистите ресурсы после выполнения действий из этого учебника. В этом случае работа с этим учебником не приведет к превышению квоты бесплатной учетной записи.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Установка и вход

1. Перетащите следующую кнопку в запущенную рабочую область Eclipse, чтобы установить подключаемый модуль Azure Toolkit for Eclipse ([другие варианты установки](installation.md)).

    [![Перетаскивание запущенной рабочей области Eclipse*. *Требуется клиент Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Перетаскивание запущенной рабочей области Eclipse*. *Требуется клиент Eclipse Marketplace")

1. Чтобы войти в учетную запись Azure, щелкните **Tools** (Средства), выберите **Azure** и **Sign In** (Вход).
   ![Меню Eclipse для входа в систему Azure][I01]

1. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход) ([другие варианты входа](sign-in-instructions.md)).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

1. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][I03]

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][I04]

1. Наконец, в диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I05]

## <a name="creating-web-app-project"></a>Создание проекта веб-приложения

1. Выберите **File** (Файл), **New** (Создать), а затем — **Dynamic Web Project** (Динамический веб-проект). (Если элемента **Динамический веб-проект** нет в списке доступных проектов после выбора пунктов **Файл** и **Создать**, сделайте следующее: последовательно щелкните **Файл**, **Создать**, **Проект...** , разверните узел **Интернет**, щелкните **Dynamic Web Project** (Динамический веб-проект) и нажмите кнопку **Далее**.)

   ![Создание динамического веб-проекта][file-new-dynamic-web-project]

2. Для целей данного учебника присвойте проекту имя **MyWebApp**. Экран будет выглядеть следующим образом:
   
   ![Свойства нового динамического веб-проекта][dynamic-web-project-properties]

3. Нажмите кнопку **Готово**.

4. В представлении обозревателя проектов в Eclipse разверните узел **MyWebApp**. Щелкните правой кнопкой мыши **WebContent**, выберите **Создать**, затем щелкните **JSP File** (JSP-файл).

   ![Создание файла JSP][create-new-jsp-file]

5. В диалоговом окне **New JSP File** (Новый JSP-файл) укажите имя файла **index.jsp**, оставьте родительскую папку **MyWebApp/WebContent** без изменений, а затем нажмите кнопку **Далее**.

   ![Диалоговое окно New JSP File (Создание JSP-файла)][new-jsp-file-dialog]

6. Для нашего примера в диалоговом окне **Select JSP Template** (Выбор шаблона JSP) щелкните **New JSP File (html)** (Создать JSP-файл (html)) и нажмите кнопку **Готово**.

   ![Выбор шаблона JSP][select-jsp-template]

7. При открытии в Eclipse файла index.jsp добавьте код для динамического отображения фразы **Hello World!** в существующий элемент `<body>`. Обновленное содержимое элемента `<body>` должно выглядеть так:
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. Сохраните index.jsp.

## <a name="deploying-web-app-to-azure"></a>Развертывание веб-приложения в Azure

1. В представлении обозревателя проектов Eclipse щелкните правой кнопкой мыши проект, выберите **Azure**, а затем — **Publish as Azure Web App** (Опубликовать как веб-приложение Azure).
   
   ![Опубликовать как веб-приложение Azure][publish-as-azure-web-app]

1. В диалоговом окне **Deploy Web App** (Развертывание веб-приложения) можно выбрать один из следующих вариантов:

   * Выберите существующее веб-приложение.

      ![Выбор службы приложения][select-app-service]

   * Щелкните **Create New Web App** (Создать веб-приложение).

      ![Создание службы приложений][create-app-service]

      В диалоговом окне **Create App Service** (Создание службы приложений) укажите требуемую информацию для веб-приложения и нажмите кнопку **Create** (Создать).

      Здесь вы можете настроить среду выполнения, параметры приложения, план службы и группу ресурсов.

      ![Диалоговое окно "Создание службы приложений"][create-app-service-dialog]

1. Выберите веб-приложение и щелкните **Deploy** (Развернуть).

   ![Развертывание службы приложений][deploy-app-service]

1. Набор средств отобразит сообщение о состоянии (**Опубликовано**) на вкладке **Журнал действий Azure** при успешном развертывании веб-приложения, где будет содержаться гиперссылка с URL-адресом развернутого веб-приложения.

   ![Состояние публикации][publish-status]

1. Перейти к своему веб-приложению можно с помощью ссылки, предоставленной в сообщении о состоянии.

   ![Просмотр веб-приложения][browse-web-app]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="cleaning-up-resources"></a>Очистка ресурсов

1. Опубликовав веб-приложение в Azure, вы можете управлять им, щелкнув его правой кнопкой мыши в Azure Explorer и выбрав один из параметров контекстного меню. Например, вы можете **удалить** веб-приложение здесь, чтобы очистить ресурс для этого учебника.

   ![Управление службой приложений][manage-app-service]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Обзор веб-приложений]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md

<!-- IMG List -->
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[file-new-dynamic-web-project]: media/create-hello-world-web-app/file-new-dynamic-web-project.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[create-new-jsp-file]: media/create-hello-world-web-app/create-new-jsp-file.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[select-jsp-template]: media/create-hello-world-web-app/select-jsp-template.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[deploy-web-app-dialog]: media/create-hello-world-web-app/deploy-web-app-dialog.png
[select-app-service]: media/create-hello-world-web-app/select-app-service.png
[create-app-service-dialog]: media/create-hello-world-web-app/create-app-service-dialog.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[create-app-service]: media/create-hello-world-web-app/create-app-service.png
[deploy-app-service]: media/create-hello-world-web-app/deploy-app-service.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png