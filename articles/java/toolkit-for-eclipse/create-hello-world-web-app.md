---
title: Создание веб-приложения Hello World для Службы приложений Azure с помощью Eclipse
description: В этом учебнике показано, как с помощью набора средств Azure для Eclipse создать веб-приложение Hello World для Azure.
services: app-service
keywords: java, eclipse, web app, azure app service, hello world, quick start
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 08/25/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 053bca7e41ef9c95a8ad0e20b40d682552d0b384
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534529"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Создание веб-приложения Hello World для Службы приложений Azure с помощью Eclipse

В этой статье приведены действия, необходимые для создания базового веб-приложения Hello World и его публикации в Службе приложений Azure с помощью [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse).

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

Следующие шаги описывают процесс входа в Azure в среде разработки Eclipse.

1. Если вы еще не установили подключаемый модуль, воспользуйтесь статьей [Установка Azure Toolkit for Eclipse](installation.md).

1. Чтобы войти в учетную запись Azure, щелкните **Tools** (Средства), выберите **Azure** и **Sign In** (Войти).

   :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Вход в Azure из интегрированной среде разработки Eclipse":::.

1. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход) ([другие варианты входа](sign-in-instructions.md)).

1. В диалоговом окне **Azure Device Login** (Вход на устройство Azure) щелкните **Copy&Open** (Копировать и открыть).

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

1. Выберите нужную учетную запись Azure и выполните все необходимые для входа процедуры аутентификации.

1. Завершив вход, закройте браузер и вернитесь в интегрированную среду разработки Eclipse. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и щелкните **ОК**.

### <a name="install-required-software-optional"></a>Установка необходимого программного обеспечения *(необязательно)*

Чтобы обеспечить наличие необходимых компонентов для работы с проектами веб-приложений, выполните следующие действия:

1. В меню **Help** (Справка) выберите пункт **Install New Software** (Установить новое программное обеспечение).

1. В диалоговом окне **Available Software** (Доступное программное обеспечение) щелкните **Manage** (Управление) и убедитесь, что здесь выбрана последняя версия Eclipse (например, *2020-06*).

1. Щелкните **Apply and Close** (Применить и закрыть). Разверните раскрывающееся меню *Work with:* (Работать с), чтобы отобразить рекомендуемые сайты. Выберите сайт с последней версией Eclipse, чтобы запросить доступное программное обеспечение.

1. Прокрутите список вниз и выберите элемент**Web, XML, Java EE and OSGi Enterprise Development** (Корпоративная разработка для веб-сайтов, XML, Java EE и OSGi). Щелкните **Далее**.

1. В окне сведений об установке щелкните **Далее**.

1. В диалоговом окне Просмотр лицензий ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, установите переключатель **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем нажмите кнопку **Готово**. 

   > [!NOTE]
   > Ход установки можно проверить в правом нижнем углу рабочей области Eclipse.

1. При появлении запроса на перезапуск Eclipse для завершения установки нажмите кнопку **Перезапустить сейчас**.

## <a name="creating-a-web-app-project"></a>Создание проекта веб-приложения

1. В меню **File** (Файл) разверните элемент **New** (Создать), а затем щелкните **Project** (Проект). В диалоговом окне New Project (Новый проект) разверните элемент **Web** (Интернет), выберите **Dynamic Web Project** (Динамический веб-проект) и нажмите **Далее**.

   > [!TIP]
   > Если в списке доступных проектов отсутствует элемент **Web** (Интернет), выполните действия из [этого раздела](#install-required-software-optional) и проверьте, есть ли у вас необходимое программное обеспечение Eclipse.

1. Для целей данного учебника присвойте проекту имя **MyWebApp**. Экран будет выглядеть следующим образом:
   
   ![Свойства нового динамического веб-проекта][dynamic-web-project-properties]

1. Нажмите кнопку **Готово**.

1. На панели обозревателя пакетов с левой стороны разверните узел **MyWebApp**. Щелкните правой кнопкой мыши элемент **WebContent**, наведите указатель мыши на **New** (Создать) и выберите **Other...** (Другое).

1. Разверните узел **Web** (Интернет) и найдите в нем вариант **JSP File** (JSP-файл). Щелкните **Далее**.

1. В диалоговом окне **New JSP File** (Новый JSP-файл) укажите имя файла **index.jsp**, оставьте родительскую папку **MyWebApp/WebContent** без изменений, а затем нажмите кнопку **Далее**.

   ![Диалоговое окно New JSP File (Создание JSP-файла)][new-jsp-file-dialog]

1. Для нашего примера в диалоговом окне **Select JSP Template** (Выбор шаблона JSP) щелкните **New JSP File (html 5)** (Создать JSP-файл (html 5)) и нажмите кнопку **Готово**.

1. При открытии в Eclipse файла index.jsp добавьте код для динамического отображения фразы **Hello World!** в существующий элемент `<body>`. Обновленное содержимое элемента `<body>` должно выглядеть так:
   
   ```jsp
   <body>
   <b><% out.println("Hello World!"); %></b>
   </body>
   ```
1. Сохраните index.jsp.

## <a name="deploying-the-web-app-to-azure"></a>Развертывание веб-приложения в Azure

1. На панели обозревателя пакетов с левой стороны щелкните правой кнопкой мыши проект, выберите **Azure**, а затем **Publish as Azure Web App** (Опубликовать как веб-приложение Azure).
   
   ![Опубликовать как веб-приложение Azure][publish-as-azure-web-app]

1. В диалоговом окне **Deploy Web App** (Развертывание веб-приложения) можно выбрать один из следующих вариантов:

   * Выберите существующее веб-приложение.

   * Если у вас нет существующего веб-приложения, щелкните **Create** (Создать).

      Здесь вы можете настроить среду выполнения, группу ресурсов для плана Службы приложений и параметры приложения. При необходимости создайте новые ресурсы.

      В диалоговом окне **Create App Service** (Создание службы приложений) укажите требуемую информацию для веб-приложения и нажмите кнопку **Create** (Создать).

1. Выберите веб-приложение и щелкните **Deploy** (Развернуть).

1. Набор средств отобразит сообщение о состоянии (**Опубликовано**) на вкладке **Журнал действий Azure** при успешном развертывании веб-приложения, где будет содержаться гиперссылка с URL-адресом развернутого веб-приложения.

1. Перейти к своему веб-приложению можно с помощью ссылки, предоставленной в сообщении о состоянии.

   ![Просмотр веб-приложения][browse-web-app]

## <a name="cleaning-up-resources"></a>Очистка ресурсов

1. Опубликовав веб-приложение в Azure, вы можете управлять им, щелкнув его правой кнопкой мыши в Azure Explorer и выбрав один из параметров контекстного меню. Например, вы можете **удалить** веб-приложение здесь, чтобы очистить ресурс для этого учебника.

   ![Управление службой приложений][manage-app-service]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

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

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png
