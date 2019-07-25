---
title: Добавление корневого сертификата для Azure в хранилище ЦС Java
description: Узнайте, как добавить корневой сертификат центра сертификации (ЦС) в хранилище сертификатов ЦС Java (cacerts) для использования в Microsoft Azure.
services: ''
documentationcenter: java
author: bmitchell287
manager: douge
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/13/2018
ms.author: brendm
ms.openlocfilehash: 519f75b637e957a7207a25359457252097efafbb
ms.sourcegitcommit: 4cc7f5e1e4601065bfcb4c2eeb7d47ad0bec61f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68431036"
---
# <a name="adding-a-root-certificate-to-the-java-ca-certificates-store"></a>Добавление корневого сертификата в хранилище сертификатов ЦС Java

Приложения, использующие службы Azure (например, Служебную шину Azure), должны доверять корневому сертификату Baltimore CyberTrust. Этот сертификат уже может быть установлен в вашей системе. Если это не так, из этого руководства вы узнаете, как с помощью средства Oracle **keytool** добавить необходимый корневой сертификат центра сертификации (ЦС) в хранилище сертификатов ЦС Java (cacerts). Добавленный сертификат будет использоваться для служб Azure.

Служебная программа keytool от Oracle — это _средство управления ключами и сертификатами_, позволяющее разработчикам управлять списком доверенных сертификатов, которые можно использовать в Java. Чтобы добавить сертификат ЦС перед сжатием JDK и добавлением его в папку **approot** проекта Azure, можно использовать keytool (инструмент для работы с ключами) или выполнить задачу запуска Azure, где keytool используется для добавления сертификата.

15 апреля 2013 года в системе Azure начался переход с корневого сертификата GTE CyberTrust Global на Baltimore CyberTrust. Далее в этой статье рассказывается, как с помощью средства keytool добавить корневой сертификат Baltimore CyberTrust в хранилище сертификатов ЦС Java (cacerts).

> [!NOTE]
> 
> Инструкции в этой статье можно использовать для настройки в пакете SDK для Java доверия к корневым сертификатам из других доверенных центров сертификации. Например, можно выбрать корневой сертификат из списка [корневых сертификатах GeoTrust](https://www.geotrust.com/resources/root-certificates/).
> 

## <a name="determining-which-root-certificates-are-installed"></a>Определение установленных сертификатов

Сертификат Baltimore, возможно, уже установлен в вашем хранилище cacerts, поэтому сначала нужно определить, установлен ли он. Для этого сделайте вот что:

1. В командной строке администратора перейдите в папку JDK **jdk\jre\lib\security** и выполните приведенную ниже команду, которая возвращает список сертификатов, установленных на компьютере.

   ```shell
   keytool -list -keystore cacerts
   ```

1. Если появится запрос ввести пароль хранилища, по умолчанию используется пароль **changeit**.

   > [!NOTE]
   > 
   > Сведения о том, как изменить пароль, см. в документации по keytool: <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.
   > 

1. Если в списке нет сертификата с отпечатком `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`, скачайте и установите его, как описано ниже.

## <a name="to-add-a-root-certificate-to-the-cacerts-store"></a>Добавление корневого сертификата в хранилище cacerts

1. Скачайте корневой сертификат Baltimore CyberTrust по ссылке <https://cacert.omniroot.com/bc2025.crt> и сохраните его в папке **jdk\jre\lib\security** как файл с расширением **.cer**. Предположим, что вы скачали корневой сертификат Baltimore CyberTrust как файл **bc2025.cer**.

   > [!NOTE]
   > 
   > Корневой сертификат Baltimore CyberTrust имеет серийный номер `02:00:00:b9` и отпечаток SHA1 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.
   > 

2. Импортируйте сертификат в хранилище cacerts с помощью следующей команды:

   ```shell
   keytool -keystore cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```
   Описание

   |  Параметр   |                              ОПИСАНИЕ                               |
   |--------------|------------------------------------------------------------------------|
   | `keystore`   | Указывает хранилище сертификатов.                                       |
   | `importcert` | Указывает, что сертификат импортируется.                        |
   | `alias`      | Указывает псевдоним для сертификата.                                |
   | `file`       | Указывает имя файла для импортируемого корневого сертификата. |


3. Если появится запрос подтвердить доверие к сертификату, проверьте отпечаток. Если отпечаток — `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`, введите **y** для подтверждения.

4. Выполните следующую команду, чтобы убедиться, что сертификат СЦ был успешно импортирован:

   ```shell
   keytool -list -keystore cacerts
   ```

Добавив в JDK корневой сертификат, заархивируйте содержимое JDK и добавьте его в папку **approot** своего проекта в Azure.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о служебной программе keytool см. по ссылке <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

Дополнительные сведения о Java см. в разделе [Azure for Java developers](/azure/java) (Azure для разработчиков Java).

<!-- For more information about the root certificates used by Azure, see [Azure Root Certificate Migration](https://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx). -->

Дополнительные сведения о поддерживаемых версиях JDK, доступных для разработки в Azure, см. в статье <https://aka.ms/azure-jdks>.