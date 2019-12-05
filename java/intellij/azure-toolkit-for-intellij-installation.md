---
title: Установка набора средств Azure для IntelliJ
description: Узнайте, как установить набор средств Azure для подключаемого модуля IntelliJ, чтобы создавать и развертывать облачные приложения в Azure.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 5b5cb3663b2dbb6c699635d1d58b077d851b2657
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74810921"
---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Установка набора средств Azure для IntelliJ

В набор средств Azure для IntelliJ входят шаблоны и функции для простого создания, разработки, тестирования и развертывания облачных приложений в Azure с помощью среды разработки IntelliJ IDEA.

> [!NOTE] 
> 
> Набор средств Azure для IntelliJ — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

Установить набор средств Azure для IntelliJ можно с помощью диалогового окна **Параметры** или меню **Настройка** на начальном экране. Оба способа установки будут описаны ниже.

## <a name="prerequisites"></a>Предварительные требования

Azure Toolkit for IntelliJ требует наличия следующих программных компонентов:

* Установленный Пакет SDK для Java (JDK) 8+, например: [OpenJDK](https://openjdk.java.net/) или [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Установленный [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition или Community Edition.

> [!NOTE]
> 
> На сайте репозитория подключаемых модулей JetBrains на странице [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) приведен список сборок, совместимых с набором средств.
> 

<!--
> [!IMPORTANT]
> 
> If you are using the Azure Toolkit for IntelliJ on Windows, the toolkit requires installing the Azure SDK 2.9.6 or later in order to use the Azure emulator. You have two options for installing the Azure SDK:
> 
> * You can download and install the Azure SDK by using the [Web Platform Installer (WebPI)](https://go.microsoft.com/fwlink/?LinkID=252838).
> * If you do not have the Azure SDK installed when you create your first Azure deployment project, you will be prompted to automatically download install the requisite version of the Azure SDK.
> 
> Note that the Azure SDK is only required on Windows.
> 
-->


## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Установка набора средств Azure для IntelliJ из диалогового окна параметров

1. Запустите IntelliJ IDEA.

1. Когда откроется IntelliJ IDEA, щелкните **Файл** и выберите **Параметры**.
   
   ![Откройте диалоговое окно параметров IntelliJ IDEA][01a]

1. В диалоговом окне "Параметры" нажмите кнопку **Подключаемые модули**, а затем **Browse repositories** (Обзор репозиториев).
   
   ![Диалоговое окно параметров IntelliJ IDEA][02a]

1. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**.
   
   ![Поиск набора средств Azure для IntelliJ][03]
   
   Ход установки IntelliJ IDEA отобразится в диалоговом окне.
   
   ![Ход установки][04]

1. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.
   
   ![Перезапуск IntelliJ IDEA][05]

1. Нажмите кнопку **ОК** , чтобы закрыть диалоговое окно параметров.
   
   ![Закройте диалоговое окно параметров IntelliJ IDEA][06]

1. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
1   ![Перезапуск IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Установка набора средств Azure для IntelliJ с начального экрана

1. Запустите IntelliJ IDEA.

1. Когда откроется начальный экран IntelliJ IDEA, нажмите кнопку **Настройка** и выберите **Подключаемые модули**.
   
   ![Подключаемые модули IntelliJ IDEA][01b]

1. В диалоговом окне **Подключаемые модули** нажмите кнопку **Browse repositories** (Обзор репозиториев).
   
   ![Обзор репозиториев подключаемых модулей IntelliJ IDEA][02b]

1. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**.
   
   ![Поиск набора средств Azure для IntelliJ][03]
   
   Ход установки IntelliJ IDEA отобразится в диалоговом окне.
   
   ![Ход установки][04]

1. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.
   
   ![Перезапуск IntelliJ IDEA][05]

1. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
   ![Перезапуск IntelliJ IDEA][07]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[01a]: media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: media/azure-toolkit-for-intellij-installation/07-restart-intellij.png
