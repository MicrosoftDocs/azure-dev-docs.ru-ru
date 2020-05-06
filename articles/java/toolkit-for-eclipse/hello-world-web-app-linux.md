---
title: Развертывание приложения Hello World в облачном контейнере Linux
titleSuffix: Azure Toolkit for Eclipse
description: Запустите базовое веб-приложение Hello World в контейнере Linux и разверните его в облаке с помощью Azure Toolkit for Eclipse.
services: app-service\web
documentationcenter: java
ms.date: 12/20/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 9048fe69be5f12b19b547e275fd801733849a87f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82209827"
---
# <a name="deploy-a-hello-world-web-app-to-a-linux-container-in-the-cloud-using-the-azure-toolkit-for-eclipse"></a>Развертывание веб-приложения Hello World в контейнере Linux в облаке с помощью Azure Toolkit for Eclipse

Контейнеры [Docker] широко используются при развертывании веб-приложений. При этом разработчики могут объединить все зависимости и файлы проекта в одном пакете, а затем развернуть его на сервере. Набор средств Azure Toolkit for Eclipse упрощает этот процесс для разработчиков на языке Java, предоставляя функции для развертывания контейнеров в Microsoft Azure.

В этой статье показаны действия, необходимые для создания базового веб-приложения Hello World и его публикации в контейнере Linux в Azure с помощью Azure Toolkit for Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* Клиент [Docker].

> [!NOTE]
>
> Для работы с этим руководством необходимо настроить [Docker], чтобы он имел возможность предоставить управляющую программу на порте 2375 без протокола TLS. Этот параметр можно настроить при установке Docker или с помощью меню параметров Docker.
>
> ![Меню параметров Docker][docker-settings-menu]
>

## <a name="create-a-new-web-app-project"></a>Создание проекта веб-приложения

1. Запустите Eclipse и войдите в свою учетную запись Azure, следуя [инструкциям по входу для Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions).

1. Откройте меню **File** (Файл), выберите **New** (Создать), а затем — **Dynamic Web Project** (Динамический веб-проект).
   
   ![Создание проекта][file-new-project]

1. В диалоговом окне **New Dynamic Web Project** (Создание динамического веб-проекта) введите имя и расположение проекта, а затем нажмите кнопку **Finish** (Готово).
   
   ![Указание имени проекта][project-name]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Создание реестра контейнеров Azure для использования в качестве частного реестра Docker

Ниже рассмотрена процедура использования портала Azure для создания реестра контейнеров Azure.

> [!NOTE]
>
> Если вы хотите использовать Azure CLI, а не портал Azure, выполните процедуру, описанную в разделе [Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0][Create Docker Registry using Azure CLI].
>

1. Перейдите на [портал Azure] и выполните вход.

   После входа в свою учетную запись на портале Azure можно выполнить процедуру, описанную в статье [Создание частного реестра контейнеров Docker с помощью портала Azure], которую здесь полезно представить еще раз.

1. Щелкните значок меню **+ Создать ресурс**, а затем последовательно выберите **Контейнеры** и **Реестр контейнеров**.
   
   ![Создание нового реестра контейнеров Azure][create-container-registry-01]

1. При появлении страницы **Создать реестр контейнеров** введите данные в поля **Имя реестра** и **Группа ресурсов**, выберите **Включить** для параметра **Пользователь-администратор** и нажмите кнопку **Создать**.

   ![Настройка параметров реестра контейнеров Azure][create-container-registry-02]

## <a name="deploy-your-web-app-in-a-docker-container"></a>Развертывание веб-приложения в контейнере Docker

1. Щелкните правой кнопкой мыши проект в обозревателе проектов, выберите **Azure**, а затем — **Add Docker Support** (Добавление поддержки Docker).

   Файл Docker будет автоматически создан с конфигурацией по умолчанию.

   ![Добавление поддержки Docker][add-docker-support]

1. После добавления поддержки Docker щелкните проект правой кнопкой мыши в обозревателе проектов, выберите **Azure**, а затем — **Publish to Web App for Containers** (Опубликовать в веб-приложении для контейнеров).

   ![Публикация в веб-приложении для контейнеров][run-on-web-app-for-containers]

1. Когда отобразится диалоговое окно **Run on Web App for Containers** (Выполнение в веб-приложении для контейнеров), введите необходимые сведения:

   * **Docker File** (Файл Docker). Путь к файлу Docker, который был создан при добавлении поддержки Docker к проекту. 

   * **Container Registry** (Реестр контейнеров). Выберите в раскрывающемся меню реестр контейнеров, который вы создали в предыдущем разделе этой статьи. Поля **Server URL** (URL-адрес сервера), **Username** (Имя пользователя) и **Password** (Пароль) будут заполнены автоматически.

   * **Image and tag** (Образ и тег). Имя образа контейнера. Обычно используется следующий синтаксис: *registry*.azurecr.io/*appname*:latest, где: 
      * *registry* — это реестр контейнеров из предыдущего раздела этой статьи; 
      * *appname* — это имя веб-приложения. 

   * **Web App for Container** (Веб-приложение для контейнера). Выберите **Use Existing** (Использовать существующее) или **Create New** (Создать), чтобы указать, будете ли вы развертывать контейнер в существующем веб-приложении или создадите новое.  С помощью указанного вами **имени приложения** будет создан URL-адрес для вашего веб-приложения, например: *wingtiptoys.azurewebsites.net*.

   * **Группа ресурсов**. Указывает, создадите ли вы группу ресурсов или будете использовать существующую. 

   * **App Service Plan** (План службы приложений). Указывает, создадите ли вы план службы приложений или будете использовать существующий. 

   ![Выполнение в веб-приложении для контейнеров][run-on-web-app-linux]

1. Настроив перечисленные выше параметры, нажмите кнопку **OK**, чтобы опубликовать свое веб-приложение в Azure.

1. После публикации веб-приложения вы можете перейти к указанному для него ранее URL-адресу, например *wingtiptoys.azurewebsites.net*.

   ![Переход к веб-приложению][browsing-to-web-app]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные материалы по Docker доступны на официальном [веб-сайте Docker][Docker].

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Портал Azure]: https://portal.azure.com/
[Создание частного реестра контейнеров Docker с помощью портала Azure]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: /azure/developer/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[add-docker-support]: media/hello-world-web-app-linux/add-docker-support.png
[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[create-container-registry-01]: media/hello-world-web-app-linux/create-container-registry-01.png
[create-container-registry-02]: media/hello-world-web-app-linux/create-container-registry-02.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png
[file-new-project]: media/hello-world-web-app-linux/file-new-project.png
[project-name]: media/hello-world-web-app-linux/project-name.png
[run-on-web-app-for-containers]: media/hello-world-web-app-linux/run-on-web-app-for-containers.png
[run-on-web-app-linux]: media/hello-world-web-app-linux/run-on-web-app-linux.png
