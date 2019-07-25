---
title: Отображение в Eclipse содержимого Javadoc для пакета библиотек Azure для Java
description: Узнайте, как отобразить содержимое Javadoc для библиотек Azure в Eclipse.
services: ''
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.author: brendm
ms.date: 02/01/2018
ms.devlang: Java
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: na
ms.openlocfilehash: b25feaeae2a38bbf6cbbbeef94ee40718956b85a
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68429379"
---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Отображение в Eclipse содержимого Javadoc для пакета библиотек Azure для Java

Содержимое Javadoc для библиотек Azure для Java можно просмотреть в среде Eclipse, сопоставив его с библиотеками Azure для Java. Ниже показано, как использовать эту функциональность в Eclipse.

В данной процедуре подразумевается, что вы уже добавили библиотеку Azure для Java в путь сборки.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Порядок отображения в Eclipse содержимого Javadoc для библиотек Azure для Java

1. В разделе проекта **Referenced Libraries** (Ссылки на библиотеки) обозревателя проектов Eclipse откройте контекстное меню для JAR-файла библиотеки Azure для Java. Например, **microsoft windowsazure-api 0.1.0.jar** (номер версии может отличаться в зависимости от установленной версии).

1. Щелкните **Свойства**.

1. В левой области диалогового окна **Свойства** щелкните **Javadoc Location** (Расположение Javadoc). Отображается диалоговое окно **Javadoc Location** (Расположение Javadoc).

1. Вы можете указать **Javadoc URL** (URL-адрес Javadoc) или **Javadoc in archive** (Javadoc в архиве) в соответствующих полях.

   * Если вам требуется указать **URL-адрес Javadoc**, используйте URL-адреса в следующем формате: **http://dl.windowsazure.com/javadoc** или **http://dl.windowsazure.com/storage/javadoc** .

   * Если вы решите использовать **Javadoc in archive**(Javadoc в архиве), можете указать внешний файл или файл рабочей области.

   Выберите нужный вариант и при необходимости выполните переход или проверку. В следующем примере библиотеки Azure для Java сопоставляются с соответствующим JAR-файлом Javadoc, скачанным в локальную папку с именем **c:\MyAzureJARs**.

   ![Отображение содержимого Javadoc.][ic553487]

1. *Необязательный шаг*. Щелкните **Проверить**. При этом могут отображаться потенциальные проблемы с JAR-файлом Javadoc.

1. Последовательно выберите **ОК**.

После сопоставления с библиотекой содержимое Javadoc должно отображаться в Eclipse IDE. Например, если `blob` определен в коде с типом `CloudBlockBlob`, ниже приведен пример содержимого Javadoc, которое появляется при вводе `blob.acquireLease` в коде:

![Отображение содержимого Javadoc.][ic553488]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

<!-- IMG List -->

[ic553487]: media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png
