---
title: Средства Azure для разработчиков Java
description: Средства интеграции IDE, эмуляторы, обозреватели ресурсов и интерфейсы командной строки для разработчиков Java, работающих со службами Azure.
author: rloutlaw
manager: douge
ms.assetid: b55923b7-d60a-460d-b77c-af5fac67f1cc
ms.devlang: java
ms.topic: article
ms.service: azure
ms.date: 11/13/2018
ms.author: brendm
ms.custom: seo-java-july2019
ms.openlocfilehash: 88e587bf28c33c0499667aae89b6b44cd0fa919e
ms.sourcegitcommit: f799dd4590dc5a5e646d7d50c9604a9975dadeb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68691971"
---
# <a name="azure-tools-for-java-developers"></a>Средства Azure для разработчиков Java

## <a name="supported-jdk-runtimes"></a>Поддерживаемые среды выполнения JDK

В Azure и Azure Stack разработчики Java могут собирать и запускать производственные приложения на Java 7, 8 и 11 с помощью [сборок OpenJDK под названием Zulu Enterprise от Azul Systems](https://www.azul.com/downloads/azure-only/zulu/), не неся при этом дополнительных затрат на поддержку. Если сейчас вы используете другие пакеты JDK в своих приложениях на Java, рассмотрите возможность перехода на Zulu в Azure, чтобы получить бесплатную поддержку и обслуживание. 

Дополнительные сведения о поддерживаемых версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.

## <a name="eclipse-and-intellij-plugins"></a>Подключаемые модули Eclipse и IntelliJ

Управляйте ресурсами Azure и развертывайте приложения из среды IDE с помощью наборов средств Azure для [Eclipse](eclipse/azure-toolkit-for-eclipse.md) и [IntelliJ](intellij/azure-toolkit-for-intellij.md).   

![Набор средств IntelliJ с Azure Explorer](media/intelliJ-azure-explorer.png)

[Начало работы с набором средств Azure для Eclipse](https://docs.microsoft.com/azure/app-service-web/app-service-web-eclipse-create-hello-world-web-app) | [Начало работы с набором средств Azure для IntelliJ](https://docs.microsoft.com/azure/app-service-web/app-service-web-intellij-create-hello-world-web-app) 

## <a name="visual-studio-code"></a>Visual Studio Code

[VS Code](https://code.visualstudio.com/) — это легковесный, но в то же время мощный редактор кода, доступный для MacOS, Windows и Linux. VS Code поддерживает простой и современный рабочий процесс разработки с помощью Java, предлагая ряд расширений, которые облегчают навигацию и работу с проектом, а также позволяют выполнять запуск, отладку и форматирование кода.

[Начало работы с VS Code и Java](https://code.visualstudio.com/docs/java)
[пакет расширений Java для VS Code](https://code.visualstudio.com/docs/java/extensions)  

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure CLI 2.0 позволяет управлять ресурсами Azure с помощью командной строки. Его можно использовать в браузере с [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) или [установить](https://docs.microsoft.com/cli/azure/install-azure-cli) в macOS, Linux или Windows и запускать из командной строки.

[Начало работы с Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)

## <a name="azure-storage-explorer"></a>Обозреватель службы хранилища Azure 

Управляйте учетными записями хранения Azure, контейнерами, большими двоичными объектами и файлами с настольного компьютера. Сейчас обозреватель хранилища Azure доступен в предварительной версии и работает в Windows, macOS и Linux.

[Приступая к работе с обозревателем службы хранилища (предварительная версия)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
