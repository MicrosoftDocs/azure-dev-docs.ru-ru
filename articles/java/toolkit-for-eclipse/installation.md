---
title: Установка набора средств Azure для Eclipse
description: Узнайте, как установить набор средств Azure для подключаемого модуля Eclipse, чтобы создавать и развертывать облачные приложения в Azure.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: be90cbf867cfbbb475e1a80655d2ef925069b90a
ms.sourcegitcommit: f460914ac5843eb7392869a08e3a80af68ab227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92010208"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Установка набора средств Azure для Eclipse

Azure Toolkit for Eclipse предоставляет функциональные возможности для быстрого создания, разработки, настройки, тестирования и развертывания в Azure нересурсоемких и масштабируемых веб-приложений Java и заданий HDInsight Spark с высоким уровнем доступности с помощью среды разработки Eclipse.

> [!NOTE] 
> 
> Набор средств Azure для Eclipse — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

Существует два способа установки Azure Toolkit for Eclipse: через магазин **Eclipse Marketplace** или с помощью пункта **Install new software** (Установить новое программное обеспечение) в меню Help (Справка). Оба способа установки будут описаны в следующих разделах.

## <a name="eclipse-marketplace"></a>Eclipse Marketplace

Мастер Eclipse Marketplace в интегрированной среде разработки Eclipse позволяет пользователям просматривать [Eclipse Marketplace](https://marketplace.eclipse.org/) и устанавливать решения. Следующие два действия позволяют перейти к магазину Eclipse Marketplace:

   * Перетащите кнопку ниже в запущенную рабочую область Eclipse. Эта кнопка открывает Eclipse Marketplace с уже выбранным вариантом Azure Toolkit for Eclipse.

      [![Перетаскивание запущенной рабочей области Eclipse*. *Требуется клиент Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Перетаскивание запущенной рабочей области Eclipse*. *Требуется клиент Eclipse Marketplace")

   * В интегрированной среде разработки Eclipse откройте меню **Help** (Справка), перейдите к **Eclipse Marketplace**, выполните поиск по строке "Azure Toolkit for Eclipse" и щелкните **Install** (Установить).

      :::image type="content" source="media/installation/eclipse-marketplace-button.png" alt-text="Окно Marketplace, меню Help (Справка)."::: 

1. Откроется окно мастера Eclipse Marketplace с инструкциями по установке и списком компонентов для установки. Убедитесь, что выбраны все нужные компоненты, и щелкните **Confirm >** (Подтвердить).

   | Функция | Описание | 
   |---|---| 
   | **Подключаемый модуль Application Insights для Java** | Этот компонент позволяет использовать службы регистрации и анализа телеметрии Azure для приложений и экземпляров серверов. | 
   | **Общий подключаемый модуль Azure** | Этот компонент предоставляет функциональные возможности, необходимые другим компонентам набора средств. | 
   | **Набор средств контейнеров Azure для Eclipse** | Этот компонент позволяет создавать и развертывать WAR-файлы как контейнеры Docker в виртуальной машине Docker. |
   | **Azure Explorer для Eclipse** | Этот компонент предоставляет интерфейс в виде проводника для управления ресурсами Azure. | 
   | **Подключаемый модуль Azure HDInsight для Java** | Поддерживает разработку приложений Apache Spark в Scala. |
   | **Microsoft JDBC Driver 6.1 для SQL Server** | Этот компонент предоставляет API JDBC для SQL Server и Базу данных SQL Microsoft Azure для платформы Java Enterprise Edition 8. | 
   | **Пакет для библиотек Microsoft Azure для Java** | Этот компонент предоставляет API-интерфейсы для доступа к таким службам Microsoft Azure, как хранилище, служебная шина, среда выполнения службы и т. д. | 
   | **Подключаемый модуль WebApp для Eclipse** | Позволяет развертывать веб-приложения как Службы приложений Azure. | 

1. В диалоговом окне **Review Licenses** (Просмотр лицензий) ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, установите переключатель **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем щелкните **Готово**. 

   > [!NOTE]
   > Ход установки можно проверить в правом нижнем углу рабочей области Eclipse.

4. Когда установка завершится, вам будет предложено перезапустить интегрированную среду разработки Eclipse, чтобы применить обновление программного обеспечения. Нажмите кнопку **Перезагрузить сейчас**.

## <a name="install-new-software"></a>Установка нового программного обеспечения

Azure Toolkit for Eclipse можно установить непосредственно из меню *Help* (Справка) в формате нового программного обеспечения.

1. В меню **Help** (Справка) выберите пункт **Install New Software** (Установить новое программное обеспечение).

   :::image type="content" source="media/installation/eclipse-install-software-button.png" alt-text="Окно Marketplace, меню Help (Справка)."::: 

1. В диалоговом окне **Available Software** (Доступное программное обеспечение) введите значение `http://dl.microsoft.com/eclipse/` в текстовое поле **Work with** (Работать с).

1. В области **Name** (Имя) установите флажок **Azure Toolkit for Java** (Набор средств Azure для Java) и снимите флажок **Contact all update sites during install to find required software** (Проверить все сайты обновления во время установки для поиска требуемого ПО). Экран должен выглядеть следующим образом:

   ![Установка набора средств Azure для Eclipse][02]

1. Если развернуть элемент **Azure Toolkit for Java**, вы увидите список компонентов, которые будут установлены, например:

   | Функция | Описание | 
   |---|---| 
   | **Подключаемый модуль Application Insights для Java** | Этот компонент позволяет использовать службы регистрации и анализа телеметрии Azure для приложений и экземпляров серверов. | 
   | **Общий подключаемый модуль Azure** | Этот компонент предоставляет функциональные возможности, необходимые другим компонентам набора средств. | 
   | **Набор средств контейнеров Azure для Eclipse** | Этот компонент позволяет создавать и развертывать WAR-файлы как контейнеры Docker в виртуальной машине Docker. |
   | **Azure Explorer для Eclipse** | Этот компонент предоставляет интерфейс в виде проводника для управления ресурсами Azure. | 
   | **Подключаемый модуль Azure HDInsight для Java** | Поддерживает разработку приложений Apache Spark в Scala. |
   | **Microsoft JDBC Driver 6.1 для SQL Server** | Этот компонент предоставляет API JDBC для SQL Server и Базу данных SQL Microsoft Azure для платформы Java Enterprise Edition 8. | 
   | **Пакет для библиотек Microsoft Azure для Java** | Этот компонент предоставляет API-интерфейсы для доступа к таким службам Microsoft Azure, как хранилище, служебная шина, среда выполнения службы и т. д. | 
   | **Подключаемый модуль WebApp для Eclipse** | Позволяет развертывать веб-приложения как Службы приложений Azure. | 

1. Щелкните **Далее**. (Если при установке набора средств возникают необычные задержки, убедитесь, что снят флажок **Contact all update sites during install to find required software** [Проверить все сайты обновления во время установки для поиска требуемого ПО].)

1. В диалоговом окне **Install Details** (Сведения об установке) нажмите кнопку **Далее**.

1. В диалоговом окне **Review Licenses** (Просмотр лицензий) ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, установите переключатель **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем нажмите кнопку **Готово**. (В следующих действиях предполагается, что вы принимаете условия лицензионных соглашений. Если вы не принимаете эти условия, завершите процесс установки.)

   > [!NOTE]
   > Ход установки можно проверить в правом нижнем углу рабочей области Eclipse.

1. При появлении запроса на перезапуск Eclipse для завершения установки нажмите кнопку **Перезапустить сейчас**.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[02]: media/installation/eclipse-installation-02.png
