---
title: Установка набора средств Azure для Eclipse
description: Узнайте, как установить набор средств Azure для подключаемого модуля Eclipse, чтобы создавать и развертывать облачные приложения в Azure.
services: ''
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.author: brendm
ms.date: 02/01/2018
ms.devlang: Java
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: na
ms.openlocfilehash: 1b6dfbb30da08083ff48d4e62a71fac4cc7e5981
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68429235"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Установка набора средств Azure для Eclipse

Существует два способа для установки Azure Toolkit for Eclipse:

  - [Eclipse Marketplace.](#eclipse-marketplace)
  - [Установка нового программного обеспечения.](#install-new-software)

> [!NOTE] 
> 
> Набор средств Azure для Eclipse — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [azure-toolkit-for-eclipse-basic-prerequisites](../includes/azure-toolkit-for-eclipse-basic-prerequisites.md)]

## <a name="eclipse-marketplace"></a>Eclipse Marketplace

1. Перетащите кнопку ниже в запущенную рабочую область Eclipse.

    [![Перетащите в запущенную рабочую область Eclipse*. * Требуется клиент Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Перетащите в запущенную рабочую область Eclipse*. * Требуется клиент Eclipse Marketplace")

2. Кроме того, вы можете найти и установить **подключаемый модуль Azure Toolkit for Eclipse** в **разделе справки или в Eclipse Marketplace**.

    ![Marketplace](./media/azure-toolkit-for-eclipse-installation/marketplace.png)

## <a name="install-new-software"></a>Установка нового программного обеспечения

1. Запустите Eclipse.

1. Щелкните меню **Help** (Справка) и выберите пункт **Install New Software** (Установить новое программное обеспечение), как показано на следующем рисунке.

   ![Установка набора средств Azure для Eclipse][01]

1. В диалоговом окне **Available Software** (Доступное программное обеспечение) в текстовом поле **Work with** (Работа с) введите `http://dl.microsoft.com/eclipse/` и нажмите клавишу **ВВОД**.

1. В области **Name** (Имя) установите флажок **Azure Toolkit for Java** (Набор средств Azure для Java) и снимите флажок **Contact all update sites during install to find required software** (Проверить все сайты обновления во время установки для поиска требуемого ПО). Экран должен выглядеть следующим образом:

   ![Установка набора средств Azure для Eclipse][02]

1. Если развернуть **набор средств Azure для Eclipse**, вы увидите список компонентов, которые будут установлены, например:

   | Функция | ОПИСАНИЕ | 
   |---|---| 
   | **Подключаемый модуль Application Insights для Java** | Этот компонент позволяет использовать службы регистрации и анализа телеметрии Azure для приложений и экземпляров серверов. | 
   | **Общий подключаемый модуль Azure** | Этот компонент предоставляет функциональные возможности, необходимые другим компонентам набора средств. | 
   | **Набор средств контейнеров Azure для Eclipse** | Этот компонент позволяет создавать и развертывать WAR-файлы как контейнеры Docker в виртуальной машине Docker. | 
   | **Контейнеры Azure для Eclipse** | Этот компонент позволяет развертывать WAR-файлы или артефакт JAR как контейнер Docker в виртуальной машине Azure. | 
   | **Azure Explorer для Eclipse** | Этот компонент предоставляет интерфейс в виде проводника для управления ресурсами Azure. | 
   | **Microsoft JDBC Driver 6.1 для SQL Server** | Этот компонент предоставляет API JDBC для SQL Server и Базу данных SQL Microsoft Azure для платформы Java Enterprise Edition 8. | 
   | **Пакет для библиотек Microsoft Azure для Java** | Этот компонент предоставляет API-интерфейсы для доступа к таким службам Microsoft Azure, как хранилище, служебная шина, среда выполнения службы и т. д. | 

1. Щелкните **Далее**. (Если при установке набора средств возникают необычные задержки, убедитесь, что снят флажок **Contact all update sites during install to find required software** [Проверить все сайты обновления во время установки для поиска требуемого ПО].)

1. В диалоговом окне **Install Details** (Сведения об установке) нажмите кнопку **Далее**.

   ![Просмотр сведений об установке][03]

1. В диалоговом окне **Review Licenses** (Просмотр лицензий) ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, установите переключатель **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем нажмите кнопку **Готово**. (В следующих действиях предполагается, что вы принимаете условия лицензионных соглашений. Если вы не принимаете эти условия, завершите процесс установки.)

   ![Review Licenses][04]

   Eclipse скачает и установит необходимые пакеты.

   ![Ход установки][05]

1. Если отображается запрос на перезапуск Eclipse для завершения установки, нажмите кнопку **Yes**(Да).

   ![Запрос перезапуска][06]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[01]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png
