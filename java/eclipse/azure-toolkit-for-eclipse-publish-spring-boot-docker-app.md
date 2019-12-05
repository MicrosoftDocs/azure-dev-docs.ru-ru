---
title: Публикация приложения Spring Boot в виде контейнера Docker
titleSuffix: Azure Toolkit for Eclipse
description: Вы можете узнать, как опубликовать веб-приложение в Microsoft Azure в виде контейнера Docker с помощью набора средств Azure для Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: ece88bc262cf6b7c626cd50364c1133b098bb0f7
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811835"
---
# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>Публикация приложения Spring Boot в виде контейнера Docker с помощью набора средств Azure для Eclipse

[Spring Framework] — это решение с открытым исходным кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java.

[Docker] — это решение с открытым исходным кодом, которое помогает разработчикам автоматизировать развертывание приложений, их масштабирование, а также управление приложениями, которые выполняются в контейнерах.

В данном руководстве описывается процесс развертывания приложения Spring Boot в качестве контейнера Docker в Microsoft Azure с помощью набора средств Azure для Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repository"></a>Клонирование репозитория по умолчанию приложения Docker Spring Boot

### <a name="import-the-public-repository"></a>Импорт общедоступного репозитория

Ниже рассмотрена процедура клонирования репозитория приложения Docker Spring Boot на локальный компьютер с помощью IntelliJ. См. подробнее об использовании командной строки в руководстве по [развертыванию приложения Spring Boot в Linux в службе контейнеров Azure][Deploy Spring Boot on Linux in AKS].

1. Откройте Eclipse.

1. Последовательно выберите **Файл** > **Импортировать**.

   ![Меню "File" (Файл) > "Import" (Импортировать)][CL01]

1. Когда откроется диалоговое окно **Import** (Импорт), сделайте следующее.

   a. Разверните элемент **Git**.

   b. Выберите **Projects from Git** (Проекты из Git).
   
   c. Щелкните **Далее**.

   ![Диалоговое окно "Import" (Импорт)][CL02]

1. На странице **Select Repository Source** (Выбор источника репозитория) сделайте следующее.

   a. Выберите **Клонировать URI**.
   
   b. Щелкните **Далее**.

   ![Страница "Выбор источника репозитория"][CL03]

1. На странице **Source Git Repository** (Исходный репозиторий Git) сделайте следующее.

   a. В поле **URI** введите `https://github.com/spring-guides/gs-spring-boot-docker.git`. Поля **Host** (Узел) и **Repository path** (Путь к репозиторию) должны автоматически заполниться правильными значениями.
   
   b. Репозиторий Spring Boot является общедоступным, поэтому нет необходимости вводить имя пользователя и пароль Git.
   
   c. Щелкните **Далее**.

   ![Страница "Исходный репозиторий Git"][CL04]

1. На странице **Branch Selection** (Выбор ветви) нажмите кнопку **Next** (Далее).

   ![Страница "Выбор ветви"][CL05]

1. На странице **Local Destination** (Локальное назначение) сделайте следующее.

   a. Укажите локальную папку, в которой должен находиться локальный репозиторий.
   
   b. Щелкните **Далее**.

   ![Страница "Локальное назначение"][CL06]

1. На странице **Select a wizard to use for importing projects** (Выбор мастера для импорта проектов) сделайте следующее.

   a. Выберите **Import as a general project** (Импортировать как универсальный проект).
   
   b. Щелкните **Далее**.

   ![На страница "Выбор мастера для импорта проектов"][CL07]

1. На странице **Import Projects** (Импорт проектов) сделайте следующее.

   a. Укажите имя проекта.
   
   b. Нажмите кнопку **Готово**

   ![Страница "Импорт проектов"][CL08]

1. Если репозиторий успешно клонирован, вы увидите полный список файлов в Eclipse.

   ![Локальный репозиторий][CL09]

### <a name="create-a-maven-project-from-your-local-repository"></a>Создание проекта Maven из локального репозитория

Репозиторий Docker Spring Boot содержит завершенный проект Maven, который будет использоваться в этом руководстве. 

1. Последовательно выберите **Файл** > **Импортировать**.

   ![Команда "Импортировать" в меню "Файл"][CL01]

1. Когда откроется диалоговое окно **Import** (Импорт), сделайте следующее.

   a. Разверните элемент **Maven**.
   
   b. Выберите **Existing Maven Projects** (Существующие проекты Maven).
   
   c. Щелкните **Далее**.

   ![Диалоговое окно "Import" (Импорт)][MV01]

1. На странице **Maven Projects** (Проекты Maven) сделайте следующее.

   a. В поле **Root Directory** (Корневой каталог) укажите папку **complete** в локальном каталоге.
   
   b. Разверните раздел **Advanced** (Дополнительно) и введите пользовательское имя в поле **Name template** (Шаблон имени).
   
   c. Установите флажок для файла **pom.xml** в проекте.
   
   d. Нажмите кнопку **Готово**

   ![Страница "Проекты Maven"][MV02]

1. После успешного открытия проекта Maven в Eclipse появится второй проект.

   ![Локальный проект Maven][MV03]

## <a name="build-your-spring-boot-app-by-using-maven"></a>Построение приложения Spring Boot с помощью Maven

1. В обозревателе проектов Eclipse выберите проект Maven.

1. Последовательно выберите **Запуск** > **Запуск от имени** > **Сборка Maven**.

   ![Команды для выполнения в качестве сборки Maven][BU01]

1. После успешной сборки приложения в окне консоли отобразится состояние.

   ![Успешное выполнение сборки Maven][BU02]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Публикация веб-приложения в Azure с помощью контейнера Docker

1. В обозревателе проектов Eclipse выберите проект Maven.

1. Щелкните меню Azure **Publish** (Публикация) и выберите **Publish as Docker container** (Опубликовать как контейнер Docker).

   ![Команда публикации в виде контейнера Docker][PU01]

1. Когда откроется диалоговое окно **Deploy Docker Container on Azure** (Развертывание контейнера Docker в Azure), сделайте следующее.

   a. Введите пользовательское имя образа Docker.
   
   b. В поле **Artifact to deploy** (Развертываемый артефакт) укажите путь к файлу **gs-spring-boot-docker-0.1.0.jar**, сборку которого вы только что выполнили.

   ![Задание параметров Docker][PU02]

   Отобразятся все существующие узлы Docker. 

1. Если требуется выполнить развертывание на существующий узел, можно сразу перейти к шагу 5. В противном случае, чтобы создать узел, выполните следующие действия.

   a. Щелкните **Добавить**.

      ![Добавление нового узла Docker][PU03]

   b. При появлении диалогового окна **Создание узла Docker** можно принять значения по умолчанию или задать пользовательские параметры для нового узла Docker. (Подробное описание различных параметров см. в руководстве по [публикации веб-приложений в виде контейнеров Docker с помощью Azure Toolkit for IntelliJ][Publish Container with Azure Toolkit].) Нажмите кнопку **Next** (Далее) после задания используемых параметров.

      ![Задание параметров узла Docker][PU04]

   c. Можно использовать для входа в систему существующие учетные данные, хранящиеся в Azure Key Vault, или ввести новые учетные данные для входа в Docker. После задания параметров нажмите кнопку **Finish** (Готово).

      ![Задание учетных данных узла Docker][PU05]

1. Выберите узел Docker и нажмите кнопку **Next** (Далее).

   ![Выбор используемого узла Docker][PU06]

1. На последней странице диалогового окна **Deploy Docker Container on Azure** (Развертывание контейнера Docker в Azure) задайте следующие параметры.

   a. Можно указать пользовательское имя для контейнера, в котором будет размещаться контейнер Docker, или принять значение по умолчанию.

   b. Для узла Docker укажите TCP-порты с помощью следующего синтаксиса: *[внешний порт]* : *[внутренний порт]* . Например, **80:8080** означает внешний порт "80" и внутренний порт Spring Boot по умолчанию "8080".
   
      После настройки внутреннего порта (например, с помощью файла application.yml) необходимо указать номер порта для обеспечения корректной маршрутизации в Azure.

   c. После настройки этих параметров щелкните **Finish** (Готово).

   ![Развертывание контейнера Docker в Azure][PU07]

1. После завершения публикации набора средств Azure в журнале действий Azure будет отображаться состояние **Опубликовано**.

   ![Успешно развернутый узел Docker][PU08]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные материалы по Docker доступны на официальном [веб-сайте Docker](https://www.docker.com/).

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: https://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in AKS]: /azure/container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: azure-toolkit-for-eclipse-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png
