---
title: Установка набора средств Azure для IntelliJ
description: Узнайте, как установить набор средств Azure для подключаемого модуля IntelliJ, чтобы создавать и развертывать облачные приложения в Azure.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: fe8b07257ff3a9fc5523d13dd13e19982103ab05
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534490"
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


## <a name="from-the-settings-dialog-box"></a>В диалоговом окне "Параметры"

1. На панели инструментов IntelliJ IDEA, щелкните **Файл** и выберите **Параметры**.

1. В меню навигации слева от диалогового окна "Параметры" щелкните **Подключаемые модули**.

1. В строке поиска **Marketplace** введите "Azure", чтобы отфильтровать список подключаемых модулей. Выберите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**. Прочитайте *примечание о конфиденциальности для сторонних подключаемых модулей* IntelliJ и щелкните **Принять**.

   :::image type="content" source="media/installation/03-intellij-search-plugin.png" alt-text="Поиск подключаемого модуля для Azure Toolkit for IntelliJ."::: 

1. После завершения установки щелкните **Перезапустить IDE**.

1. При появлении запроса на перезапуск IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
   :::image type="content" source="media/installation/07-restart-intellij.png" alt-text="Перезапуск IntelliJ IDEA."::: 

## <a name="from-the-start-screen"></a>На начальном экране

1. На начальном экране IntelliJ IDEA щелкните **Настройка** и выберите **Подключаемые модули**.

   :::image type="content" source="media/installation/01-intellij-configure-dropdown.png" alt-text="Подключаемые модули на начальном экране."::: 

1. В строке поиска **Marketplace** введите "Azure", чтобы отфильтровать список подключаемых модулей. Выберите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**. Прочитайте *примечание о конфиденциальности для сторонних подключаемых модулей* IntelliJ и щелкните **Принять**.

   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace.png" alt-text="Marketplace с подключаемыми модулями на начальном экране.":::

1. После завершения установки щелкните **Перезапустить IDE**.

1. При появлении запроса на перезапуск IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace-restart.png" alt-text="Перезапуск для установки с начального экрана.":::

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

