---
title: Управление учетными записями хранения с помощью Azure Explorer для Eclipse
description: Вы можете узнать, как управлять учетными записями хранения Azure с помощью Azure Explorer для Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 131cc95ce3b927ffc26ea7b08367b65dd434c0e4
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82209767"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Управление учетными записями хранения с помощью Azure Explorer для Eclipse

Azure Explorer, входящий в состав набора средств Azure для Eclipse, предоставляет разработчикам на Java удобное решение для управления учетными записями хранения в их учетной записи Azure из интегрированной среды разработки Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Создание учетной записи хранения в Eclipse

Чтобы создать учетную запись хранения с помощью Azure Explorer, сделайте следующее:

1. Войдите в свою учетную запись Azure, следуя [инструкциям по входу для набора средств Azure для Eclipse](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions).

1. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Учетные записи хранения** и выберите **Создать учетную запись хранения**.

   ![Команда "Создать учетную запись хранения"][CS01]

1. В диалоговом окне **Создание учетной записи хранения** укажите следующие параметры.

   ![Диалоговое окно "Создание учетной записи хранения"][CS02]

   * **Имя**: указывает имя для новой учетной записи хранения.

   * **Подписка**: указывает подписку Azure, которую нужно использовать для новой учетной записи хранения.

   * **Группа ресурсов**: определяет группу ресурсов для виртуальной машины. Выберите один из следующих вариантов.
      * **Создать**: указывает, что нужно создать группу ресурсов.
      * **Использовать существующий**: указывает, что вы выберете группу ресурсов, связанную с учетной записью Azure.

   * **Регион**: указывает расположение для создания учетной записи хранения, например "западная часть США".

   * **Тип учетной записи**: указывает тип создаваемой учетной записи хранения (например, "хранилище BLOB-объектов"). Дополнительные сведения см. в статье [Об учетных записях хранения Azure].

   * **Производительность**: определяет, какое предложение по учетной записи хранения выбранного издателя нужно использовать (например, "Премиум"). Дополнительные сведения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure].

   * **Репликация**: указывает репликацию для учетной записи хранения (например, "избыточная между зонами"). Дополнительные сведения см. в статье [Репликация службы хранилища Azure].

1. Указав все перечисленные выше параметры, нажмите кнопку **Создать**.

## <a name="create-a-storage-container-in-eclipse"></a>Создание контейнера хранилища в Eclipse

Чтобы создать контейнер хранилища с помощью Azure Explorer, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши учетную запись хранения, для которой нужно создать контейнер, а затем нажмите кнопку **Создать контейнер BLOB-объектов**.

   ![Команда "Создать контейнер BLOB-объектов"][CC01]

1. В диалоговом окне **Создание контейнера BLOB-объектов** укажите имя для своего контейнера и нажмите кнопку **ОК**. Дополнительные сведения об именовании контейнеров хранилища см. в статье [Naming and Referencing Containers, Blobs, and Metadata] (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

   ![Диалоговое окно "Создание контейнера BLOB-объектов"][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Удаление контейнера хранилища в Eclipse

Чтобы удалить контейнер хранилища с помощью Azure Explorer, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши контейнер хранилища и выберите **Удалить**.

   ![Команда "Удалить контейнер хранилища"][DC01]

1. В диалоговом окне подтверждения нажмите кнопку **ОК**.

   ![Окно подтверждения удаления контейнера хранилища][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Удаление учетной записи хранения в Eclipse

Чтобы удалить учетную запись хранения с помощью Azure Explorer, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши учетную запись хранения и выберите пункт **Удалить**.

   ![Команда "Удалить учетную запись хранения"][DS01]

1. В диалоговом окне подтверждения нажмите кнопку **ОК**.

   ![Окно подтверждения удаления учетной записи хранения][DS02]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об учетных записях хранения, размерах и ценах см. в следующих ресурсах:

* [Введение в службу хранилища Microsoft Azure]
* [Об учетных записях хранения Azure]
* Размеры учетных записей хранения Azure
  * [Размеры учетных записей хранения Windows в Azure]
  * [Размеры учетных записей хранения Linux в Azure]
* Цены на учетные записи хранения Azure
  * [Windows storage-account pricing] (Цены на учетные записи хранения Windows)
  * [Linux storage-account pricing] (Цены на учетные записи хранения Linux)

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Введение в службу хранилища Microsoft Azure]: /azure/storage/common/storage-introduction
[Об учетных записях хранения Azure]: /azure/storage/storage-create-storage-account
[Репликация службы хранилища Azure]: /azure/storage/storage-redundancy
[Целевые показатели масштабируемости и производительности службы хранилища Azure]: /azure/storage/storage-scalability-targets
[Naming and Referencing Containers, Blobs, and Metadata]: https://go.microsoft.com/fwlink/?LinkId=255555 (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них)

[Размеры учетных записей хранения Windows в Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Размеры учетных записей хранения Linux в Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows storage-account pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/ (Цены на учетные записи хранения Windows)
[Linux storage-account pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/ (Цены на учетные записи хранения Linux)

<!-- IMG List -->

[CS01]: media/managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/managing-storage-accounts-using-azure-explorer/DC02.png