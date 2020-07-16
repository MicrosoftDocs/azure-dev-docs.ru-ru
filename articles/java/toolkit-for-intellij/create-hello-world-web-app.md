---
title: Создание веб-приложения Hello World для Службы приложений Azure с помощью IntelliJ
description: В этом учебнике показано, как с помощью набора средств Azure для IntelliJ создать веб-приложение Hello World для Azure.
services: app-service
keywords: java, intellij, web app, azure app service, hello world, quick start
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 47deaeb4957076ef80bcdf0f708fb16b95e0b646
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378078"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>Создание веб-приложения Hello World для Службы приложений Azure с помощью IntelliJ

С помощью подключаемого модуля с открытым кодом [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) всего за несколько минут можно создать и развернуть в Службе приложений Azure базовое приложение Hello World в качестве веб-приложения.

> [!NOTE]
>
> Если вы предпочитаете использовать Eclipse, ознакомьтесь с нашим [руководством по Eclipse][eclipse-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Обязательно очистите ресурсы после выполнения действий из этого учебника. В этом случае работа с этим учебником не приведет к превышению квоты бесплатной учетной записи.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Установка и вход

1. В диалоговом окне Settings/Preferences (Параметры и предпочтения) в IntelliJ IDEA (CTRL+ALT+S) выберите **Plugins** (Подключаемые модули). Затем найдите модуль **Azure Toolkit for IntelliJ** в **Marketplace** и щелкните **Install** (Установить). После установки щелкните **Restart** (Перезапуск), чтобы активировать подключаемый модуль. 

   ![Подключаемый модуль Azure Toolkit for IntelliJ в Marketplace][marketplace]

2. Чтобы войти в учетную запись Azure, откройте боковую панель **Azure Explorer**, а затем щелкните значок **Azure Sign In** (Вход в Azure) в верхней строке (или в меню IDEA **Tools/Azure/Azure Sign in** (Средства/Azure/Вход в Azure)).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Device Login** (Имя пользователя устройства) и щелкните **Sign in** (Вход) ([другие варианты входа](sign-in-instructions.md)).

   ![Окно Azure Sign In (Вход в Azure) с выбранным именем пользователя устройства][I02]

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Диалоговое окно входа Azure][I03]

5. В браузере вставьте код устройства (скопированный при нажатии **Copy&Open** (Копировать и открыть) на последнем шаге), а затем нажмите кнопку **Далее**.

   ![Вход в систему устройства в браузере][I04]

6. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I05]

## <a name="creating-web-app-project"></a>Создание проекта веб-приложения

1. В IntelliJ щелкните меню **File** (Файл), выберите команду **New** (Создать), а затем — **Project** (Проект).

   ![Создание проекта][file-new-project]

2. В диалоговом окне **New Project** (Новый проект) выберите **Maven**, а затем **maven-archetype-webapp** и щелкните **Next** (Далее).

   ![Выбор веб-приложения архетипа Maven][maven-archetype-webapp]

3. Укажите значение **GroupId** (Идентификатор группы) и **ArtifactId** (Идентификатор артефакта) для веб-приложения и щелкните **Next** (Далее).

   ![Указание идентификатора группы и артефакта][groupid-and-artifactid]

4. Настройте любые параметры Maven или примите значения по умолчанию и щелкните **Next** (Далее).

   ![Указание параметров Maven][maven-options]

5. Укажите имя и расположение проекта, а затем щелкните **Finish** (Готово).

   ![Указание имени проекта][project-name]

6. В представлении обозревателя проектов откройте и измените файл **src/main/webapp/index.jsp** следующим образом и **сохраните изменения**:

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```

   ![Изменение страницы индексов][edit-index-page]

## <a name="deploying-web-app-to-azure"></a>Развертывание веб-приложения в Azure

1. В представлении обозревателя проектов щелкните проект правой кнопкой мыши, разверните **Azure** и щелкните **Deploy to Azure** (Развертывание в Azure).

   ![Меню развертывания в Azure][deploy-to-azure-menu]

1. В диалоговом окне развертывания в Azure вы можете напрямую развернуть приложение в существующее веб-приложение Tomcat, если оно у вас есть. В противном случае необходимо создать такое веб-приложение.
   1. Щелкните ссылку **No Available webapp, click to create a new one** (Нет доступного веб-приложения. Щелкните, чтобы создать), чтобы создать веб-приложение. Если в подписке уже есть веб-приложения, можно выбрать **Create New WebApp** (Создать веб-приложение) из раскрывающегося списка веб-приложений.

      ![Диалоговое окно развертывания в Azure][deploy-to-azure-dialog]

   1. Во всплывающем диалоговом окне выберите **TOMCAT 8.5-jre8** как веб-контейнер и укажите другие необходимые сведения, а затем нажмите кнопку **ОК**, чтобы создать веб-приложение.

      ![Создание веб-приложения][create-new-web-app-dialog]

   1. Выберите веб-приложение из раскрывающегося списка веб-приложений, а затем щелкните **Run** (Запуск). (Вы можете выполнить запуск отсюда, если хотите выполнить развертывание в существующее веб-приложение.)

      ![Развертывание в существующее веб-приложение][deploy-to-existing-webapp]

1. Набор средств отобразит сообщение о состоянии при успешном развертывании веб-приложения, а также URL-адрес развернутого веб-приложения.

   ![Развертывание завершено успешно][successfully-deployed]

1. Перейти к своему веб-приложению можно с помощью ссылки, предоставленной в сообщении о состоянии.

   ![Просмотр веб-приложения][browse-web-app]

## <a name="managing-deploy-configurations"></a>Управление конфигурациями развертывания

1. После публикации веб-приложения параметры будут сохранены в качестве параметров по умолчанию. Развертывание можно запустить, щелкнув значок зеленой стрелки на панели инструментов. Вы можете изменить параметры, щелкнув раскрывающееся меню для веб-приложения, а затем — **Edit Configurations** (Изменить конфигурации).

   ![Пункт меню Edit configuration (Изменить конфигурацию)][edit-configuration-menu]

1. При отображении диалогового окна **Run/Debug Configurations** (Конфигурации выполнения и отладки) можно изменить любые параметры по умолчанию, а затем нажать кнопку **ОК**.

   ![Диалоговое окно Edit configuration (Изменение конфигурации)][edit-configuration-dialog]

## <a name="cleaning-up-resources"></a>Очистка ресурсов

1. Удаление веб-приложений в Azure Explorer

     ![Очистка ресурсов][clean-resources]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

<!-- URL List -->

[Azure Toolkit for IntelliJ]: azure-toolkit-for-intellij.md
[Azure Toolkit for Eclipse]: ../toolkit-for-eclipse/azure-toolkit-for-eclipse.md
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
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png
