---
title: Управление учетными записями хранения с помощью Azure Explorer для Eclipse
description: Вы можете узнать, как управлять учетными записями хранения Azure с помощью Azure Explorer для Eclipse.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 7a2a3406e43e1bc65ac61b7197399cdce08ef29b
ms.sourcegitcommit: 723441eda0eb4ff893123201a9e029b7becf5ecc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91846415"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Управление учетными записями хранения с помощью Azure Explorer для Eclipse

> [!NOTE]
> Функция "Учетные записи хранения " в Azure Explorer больше не рекомендуется к использованию. Вы можете использовать портал Azure для создания учетных записей хранения и контейнеров и управления ими. Краткие руководства по управлению учетными записями хранения см. в документации по [службе хранилища Azure](/azure/storage/blobs/storage-quickstart-blobs-portal).

Azure Explorer, входящий в состав набора средств Azure для Eclipse, предоставляет разработчикам на Java удобное решение для управления учетными записями хранения в их учетной записи Azure из интегрированной среды разработки Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

1. Войдите в свою учетную запись Azure, следуя [инструкциям по входу для набора средств Azure для Eclipse](./sign-in-instructions.md).

1. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Учетные записи хранения** и выберите **Создать учетную запись хранения**.

1. В диалоговом окне **Создание учетной записи хранения** укажите следующие параметры.

   * **Name** (Имя). Определяет имя для новой учетной записи хранения.

   * **Подписка**: определяет подписку Azure, которую нужно использовать для новой учетной записи хранения.

   * **Группа ресурсов**. Определяет группу ресурсов для виртуальной машины. Выберите один из следующих вариантов.
      * **Создать**. Определяет, что нужно создать группу ресурсов.
      * **Использовать существующую**. Определяет, что нужно выбрать группу ресурсов, связанную с учетной записью Azure.

   * **Регион**. Определяет расположение для создания учетной записи хранения, например "западная часть США".

   * **Тип учетной записи**. Определяет тип создаваемой учетной записи хранения (например "Общего назначения версии 1"). Дополнительные сведения см. в статье [Об учетных записях хранения Azure].

   * **Производительность** — Определяет, какое предложение по учетной записи хранения выбранного издателя нужно использовать (например "Стандартный"). Дополнительные сведения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure].

   * **Репликация.** Определяет репликацию для учетной записи хранения (например "Избыточная локально"). Дополнительные сведения см. в статье [Репликация службы хранилища Azure].

1. Указав все перечисленные выше параметры, нажмите кнопку **Создать**.

## <a name="create-and-manage-storage-containers"></a>Создание контейнеров хранилища и управление ими

Чтобы создать контейнеры и обеспечить управление ими, посетите портал Azure или выполните программную подготовку ресурсов.

Инструкции по использованию портала Azure и созданию контейнера в службе хранилища Azure, а также по отправке, скачиванию и выводе блочных BLOB-объектов на портале Azure см. в [этой статье](/azure/storage/blobs/storage-quickstart-blobs-portal).

## <a name="delete-a-storage-account"></a>Удаление учетной записи хранения

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши учетную запись хранения и выберите пункт **Удалить**.

1. В диалоговом окне подтверждения нажмите кнопку **ОК**.


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
[Naming and referencing containers, blobs, and metadata]: /rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata

[Размеры учетных записей хранения Windows в Azure]: /azure/virtual-machines/sizes
[Размеры учетных записей хранения Linux в Azure]: /azure/virtual-machines/sizes
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