---
title: Создание веб-приложения Hello World для Службы приложений Azure с помощью IntelliJ
description: В этом учебнике показано, как с помощью набора средств Azure для IntelliJ создать веб-приложение Hello World для Azure.
services: app-service
keywords: java, intellij, web app, azure app service, hello world, quick start
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 09/09/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: b340802d861ad1dbe1b4f118734e527cdefeec21
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831618"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>Создание веб-приложения Hello World для Службы приложений Azure с помощью IntelliJ

В этой статье показаны действия, необходимые для создания базового веб-приложения Hello World и его публикации в Службе приложений Azure с помощью [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053).

> [!NOTE]
>
> Если вы предпочитаете использовать Eclipse, ознакомьтесь с нашим [аналогичным учебником для Eclipse][eclipse-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Обязательно очистите ресурсы после выполнения действий из этого учебника. В этом случае работа с этим учебником не приведет к превышению квоты бесплатной учетной записи.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Установка и вход

Следующие шаги описывают процесс входа в Azure в среде разработки IntelliJ.

1. Если вы еще не установили подключаемый модуль, см. статью [Установка Azure Toolkit for IntelliJ](installation.md).

1. Чтобы войти в учетную запись Azure, перейдите к левой боковой панели **Azure Explorer**, а затем щелкните значок **входа в Azure**. Кроме того, можно открыть раздел **Средства**, развернуть пункт **Azure** и щелкнуть значок **входа в Azure**.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="Вход в Azure из IntelliJ."::: 

1. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход) ([другие варианты входа](sign-in-instructions.md)).

1. В диалоговом окне **Azure Device Login** (Вход на устройство Azure) щелкните **Copy&Open** (Копировать и открыть).

1. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

1. Выберите нужную учетную запись Azure и выполните все необходимые для входа процедуры аутентификации.

1. Завершив вход, закройте браузер и вернитесь в интегрированную среду разработки IntelliJ. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и щелкните **ОК**.

## <a name="creating-a-new-web-app-project"></a>Создание проекта веб-приложения

1. В меню **File** (Файл) разверните пункт **New** (Создать), а затем щелкните **Проект** (Project).

1. В диалоговом окне **New Project** (Новый проект) выберите **Maven** и убедитесь, что установлен флажок **Create from Archetype** (Создать из архетипа). В списке вариантов выберите **maven-archetype-webapp**, а затем щелкните **Далее**.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="Выбор варианта maven-archetype-webapp."::: 

1. Раскройте список **Artifact Coordinates** (Координаты артефакта), чтобы просмотреть все поля ввода и указать приведенные ниже сведения о новом веб-приложении, а затем щелкните **Далее**.

   * **Name** (Имя). Название вашего веб-приложения. Это значение автоматически помещается в поле **ArtifactId** для веб-приложения.
   * **GroupId** (Идентификатор группы). Имя группы артефактов. Как правило, это домен компании (например, *com.microsoft.azure*).
   * **Версия.** Мы сохраним здесь версию по умолчанию *1.0-SNAPSHOT*.

1. Настройте любые параметры Maven или примите значения по умолчанию и щелкните **Готово**.

1. Перейдите к проекту на вкладке **Project** (Проект) слева и откройте файл **src/Main/webapp/WEB-INF/index.jsp**. Замените его содержимое следующим кодом и **сохраните изменения**:

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="Открытие файла index.jsp.":::

## <a name="deploying-web-app-to-azure"></a>Развертывание веб-приложения в Azure

1. В представлении обозревателя проектов щелкните проект правой кнопкой мыши, разверните **Azure** и щелкните **Deploy to Azure Web Apps** (Развернуть в веб-приложения службы приложений Azure).

1. В диалоговом окне "Развертывание в Azure" можно развернуть приложение в существующем веб-приложении Tomcat или создать новое.

   a. Щелкните **No available webapp, click to create a new one** (Нет доступных веб-приложений, щелкните для создания нового), чтобы создать веб-приложение. Также можно выбрать действие **Create New WebApp** (Создать веб-приложение) из раскрывающегося списка веб-приложений, если в подписке есть существующие веб-приложения.

      :::image type="content" source="media/create-hello-world-web-app/deploy-to-azure-webapps.png" alt-text="Диалоговое окно развертывания в Azure.":::

   Во всплывающем окне **Create WebApp** (Создание веб-приложения) укажите следующие сведения и щелкните **ОК**: 

      * **Name** (Имя). Строка доменного имени для веб-приложения.
      * **Подписка**: Определяет подписку Azure, которую нужно использовать для нового веб-приложения.
      * **Platform**. Выберите *Linux*.
      * **Веб-контейнер.** Выберите *TOMCAT 9.0-jre8* или другую подходящую версию.
      * **Группа ресурсов**. Определяет группу ресурсов для веб-приложения. Вы можете выбрать существующую группу ресурсов, связанную с учетной записью Azure, или создать новую.
      * **App Service Plan** (План службы приложений). Указывает план Службы приложений для веб-приложения. Вы можете выбрать существующий план, связанный с учетной записью Azure, или создать новый.

   b. Чтобы выполнить развертывание в существующее приложение, выберите его из раскрывающегося списка веб-приложений, а затем щелкните **Run** (Запустить).

1. Набор средств отобразит сообщение о состоянии при успешном развертывании веб-приложения, а также URL-адрес развернутого веб-приложения.

1. Перейти к своему веб-приложению можно с помощью ссылки, предоставленной в сообщении о состоянии.

   :::image type="content" source="media/create-hello-world-web-app/browse-web-app.png" alt-text="Просмотр веб-приложения.":::

## <a name="managing-deploy-configurations"></a>Управление конфигурациями развертывания

> [!TIP]
> После публикации веб-приложения можно запустить развертывание, щелкнув значок зеленой стрелки на панели инструментов.

1. Перед началом развертывания веб-приложения вы можете изменить предложенные по умолчанию параметры, щелкнув раскрывающееся меню веб-приложения и выбрав **Edit Configurations** (Изменить конфигурации).

   :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="Пункт меню "Изменить конфигурацию"":::.

1. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) можно изменить любые параметры по умолчанию. Нажмите кнопку **ОК**, чтобы сохранить настройки.

## <a name="cleaning-up-resources"></a>Освобождение ресурсов

1. Чтобы удалить веб-приложение, перейдите в левую боковую панель **Azure Explorer** и найдите пункт **Веб-приложения**. 

   > [!NOTE]
   > Если пункт меню "Веб-приложения" не удается развернуть, вручную обновите список, щелкнув значок **Обновить** на панели инструментов Azure Explorer или щелкнув правой кнопкой мыши меню веб-приложения и выбрав пункт **Обновить**.

1. Щелкните правой кнопкой мыши веб-приложение, которое необходимо удалить, и выберите **Удалить**.

1. Чтобы удалить план службы приложений или группу ресурсов, перейдите на [портал Azure](https://portal.azure.com) и вручную удалите ресурсы в подписке.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

<!-- URL List -->

[Azure Toolkit for IntelliJ]: /azure/developer/java/tookit-for-intellij
[Azure Toolkit for Eclipse]: /azure/developer/java/tookit-for-eclipse
[eclipse-hello-world]: ../toolkit-for-eclipse/create-hello-world-web-app.md
[Обзор веб-приложений]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md
[intelliJ-sign-in-instructions]: sign-in-instructions.md

<!-- IMG List -->
[marketplace]:media/create-hello-world-web-app/marketplace.png
[file-new-project]: media/create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: media/create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: media/create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: media/create-hello-world-web-app/maven-options.png
[project-name]: media/create-hello-world-web-app/project-name.png
[open-index-page]: media/create-hello-world-web-app/open-index-page.png
[edit-index-page]: media/create-hello-world-web-app/edit-index-page.png
[deploy-to-azure-menu]: media/create-hello-world-web-app/run-on-web-app-menu.png
[deploy-to-azure-dialog]: media/create-hello-world-web-app/run-on-web-app-dialog.png
[deploy-to-existing-webapp]: media/create-hello-world-web-app/deploy-to-existing-webapp.png
[create-new-web-app-dialog]: media/create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: media/create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: media/create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: media/create-hello-world-web-app/edit-configuration-dialog.png
[clean-resources]: media/create-hello-world-web-app/clean-resource.png