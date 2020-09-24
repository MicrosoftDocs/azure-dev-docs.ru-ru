---
title: Управление учетными записями хранения с помощью Azure Explorer для IntelliJ
description: Вы можете узнать, как управлять учетными записями хранения Azure с помощью Azure Explorer для IntelliJ.
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: a209f532069741e9c6302bca97dc8c72cde0e396
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831950"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-intellij"></a>Управление учетными записями хранения с помощью Azure Explorer для IntelliJ

> [!NOTE]
> Функция "Учетные записи хранения " в Azure Explorer больше не рекомендуется к использованию. Вы можете использовать портал Azure для создания учетных записей хранения и контейнеров и управления ими. Краткие руководства по управлению учетными записями хранения см. в документации по [службе хранилища Azure](/azure/storage/blobs/storage-quickstart-blobs-portal).

Azure Explorer, входящий в состав набора средств Azure для IntelliJ, предоставляет разработчикам на Java удобное решение для управления учетными записями хранения в их учетной записи Azure из интегрированной среды разработки IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Чтобы создать учетную запись хранения с помощью Azure Explorer, сделайте следующее:

1. Войдите в свою учетную запись Azure, следуя [Инструкции по входу для набора средств Azure для IntelliJ]. 

2. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Учетные записи хранения** и выберите **Создать учетную запись хранения**.

3. В диалоговом окне **Создание учетной записи хранения** укажите следующие параметры.

   * **Name** (Имя). Определяет имя для новой учетной записи хранения.

   * **Тип учетной записи**. Определяет тип создаваемой учетной записи хранения (например "хранилище BLOB-объектов"). Дополнительные сведения см. в статье [Об учетных записях хранения Azure]. 

   * **Производительность** — определяет, какое предложение по учетной записи хранения выбранного издателя нужно использовать (например "Премиум"). Дополнительные сведения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure]. 

   * **Репликация.** Определяет репликацию для учетной записи хранения (например "избыточная между зонами"). Дополнительные сведения см. в статье [Репликация службы хранилища Azure]. 

   * **Подписка**: определяет подписку Azure, которую нужно использовать для новой учетной записи хранения.

   * **Расположение.** Определяет расположение для создания учетной записи хранения, например "западная часть США".

   * **Группа ресурсов**. Определяет группу ресурсов для виртуальной машины. Выберите один из следующих вариантов.
      * **Create new** (Создать). Определяет, что нужно создать группу ресурсов.
      * **Использовать существующую**. Определяет, что нужно выбрать группу ресурсов, связанную с учетной записью Azure.

4. Указав все перечисленные выше параметры, нажмите кнопку **ОК**.

## <a name="delete-a-storage-account"></a>Удаление учетной записи хранения

Чтобы удалить учетную запись хранения с помощью Azure Explorer, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши учетную запись хранения и выберите пункт **Удалить**.

2. В диалоговом окне подтверждения нажмите кнопку **Да**.


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

[Инструкции по входу для набора средств Azure для IntelliJ]: ./sign-in-instructions.md
[Введение в службу хранилища Microsoft Azure]: /azure/storage/common/storage-introduction
[Об учетных записях хранения Azure]: /azure/storage/storage-create-storage-account
[Репликация службы хранилища Azure]: /azure/storage/storage-redundancy
[Целевые показатели масштабируемости и производительности службы хранилища Azure]: /azure/storage/storage-scalability-targets
[Naming and referencing containers, blobs, and metadata]: https://go.microsoft.com/fwlink/?LinkId=255555

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