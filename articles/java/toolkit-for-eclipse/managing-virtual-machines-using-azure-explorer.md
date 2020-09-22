---
title: Управление виртуальными машинами с помощью Azure Explorer для Eclipse
description: Вы можете узнать, как управлять виртуальными машинами Azure с помощью Azure Explorer для Eclipse.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 28b0e304ee64cb24f098908e83a92d230657741f
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534624"
---
# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-eclipse"></a>Управление виртуальными машинами с помощью Azure Explorer для Eclipse

Средство Azure Explorer, входящее в состав набора средств Azure для Eclipse, предоставляет разработчикам на Java удобное решение для управления виртуальными машинами в их учетной записи Azure из интегрированной среды разработки Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Войдите в свою учетную запись Azure, следуя [инструкциям по входу для набора средств Azure для Eclipse](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions).

1. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши **Виртуальные машины** и выберите **Создать виртуальную машину**.

   :::image type="content" source="media/managing-virtual-machines-using-azure-explorer/CR01.png" alt-text="Создание варианта виртуальной машины в Azure Explorer.":::

1. В диалоговом окне **Выбор подписки** выберите подписку и нажмите кнопку **Далее**.

1. В окне **Выбор образа виртуальной машины** выберите **Расположение** (например, *Западная часть США*). Вы сможете перейти к рекомендуемому образу или выбрать собственный. В рамках этого краткого руководства мы будем использовать рекомендуемый образ. 

   Если вы выберете пользовательский образ, введите следующие данные:
   * **Издатель**: Указывает издателя, создавшего образ, который будет использоваться для создания виртуальной машины (например, *Майкрософт*).

   * **Предложение**: определяет предложение виртуальной машины выбранного издателя (например, *JDK*).

   * **SKU**. Указывает нужный номер SKU из выбранного предложения (например, *JDK_8*).

   * **Номер версии**. Указывает, какую версию из выбранного номера SKU нужно использовать.

1. Щелкните **Далее**.

1. В диалоговом окне **Основные параметры виртуальной машины** введите следующие значения.

   * **Имя виртуальной машины**. Указывает имя новой виртуальной машины, которое должно начинаться с буквы и содержать только буквы, цифры и дефисы.

   * **Size.** Указывает количество ядер и объем памяти, выделяемые для виртуальной машины.

   * **Имя пользователя**. Указывает учетную запись администратора, создаваемую для управления виртуальной машиной.

   * **Пароль**. Указывают пароль для учетной записи администратора. Повторно введите пароль в поле **Подтверждение**, чтобы подтвердить учетные данные.

1. Щелкните **Далее**.

1. В диалоговом окне **Связанные ресурсы** введите следующие значения.
   * **Группа ресурсов**. Определяет группу ресурсов для виртуальной машины. Выберите один из следующих вариантов.
      * **Create new** (Создать). Определяет, что нужно создать группу ресурсов.
      * **Использовать существующую**. Указывает, что нужно выбрать группу ресурсов, связанную с учетной записью Azure.

   * **Учетная запись хранения**. Определяет учетную запись хранения, используемую для хранения виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую.

   * **Виртуальная сеть** и **Подсеть**. Определяют виртуальную сеть и подсеть, к которым будет подключена виртуальная машина. Вы можете выбрать имеющуюся сеть и подсеть или создать их. При выборе элемента **Создать** отображается следующее диалоговое окно.

   * **Общедоступный IP-адрес**. Указывает внешний IP-адрес для виртуальной машины. Вы можете создать IP-адрес или выбрать значение **(Нет)** , если у виртуальной машины не будет общедоступного IP-адреса.

   * **Группа безопасности сети**. Определяет необязательный сетевой брандмауэр для виртуальной машины. Вы можете выбрать имеющийся брандмауэр или задать значение **(Нет)** , чтобы не использовать брандмауэр.

   * **Группа доступности**. Определяет необязательную группу доступности, в которую может входить виртуальная машина. Вы можете выбрать существующую группу доступности, создать ее или задать значение **(Нет)** , если виртуальная машина не входит в группу доступности.

10. Нажмите кнопку **Готово**.  

      > [!NOTE]
      > Ход создания можно проверить в правом нижнем углу рабочей области Eclipse.

## <a name="restart-a-virtual-machine"></a>Перезапуск виртуальной машины

Чтобы перезапустить виртуальную машину с помощью Azure Explorer в Eclipse, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Перезапустить**.

1. В диалоговом окне подтверждения нажмите кнопку **ОК**.

   ![Окно подтверждения перезапуска виртуальной машины](media/managing-virtual-machines-using-azure-explorer/RE02.png)

## <a name="shut-down-a-virtual-machine"></a>Завершение работы виртуальной машины

Чтобы завершить работу виртуальной машины с помощью Azure Explorer в Eclipse, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Завершить работу**.

1. В диалоговом окне подтверждения нажмите кнопку **ОК**.

   ![Окно подтверждения завершения работы виртуальной машины](media/managing-virtual-machines-using-azure-explorer/SH02.png)

## <a name="delete-a-virtual-machine"></a>Удаление виртуальной машины

Чтобы удалить виртуальную машину с помощью Azure Explorer в Eclipse, сделайте следующее:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Удалить**.

1. В диалоговом окне подтверждения нажмите кнопку **Да**.

   ![Окно подтверждения удаления виртуальной машины](media/managing-virtual-machines-using-azure-explorer/DE02.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о размерах виртуальных машин Azure и ценах на них см. в следующих ресурсах:

* Размеры виртуальных машин Azure
  * [Размеры виртуальных машин Windows в Azure]
  * [Размеры виртуальных машин Linux в Azure]
* Цены на виртуальные машины Azure
  * [Цены на виртуальные машины Windows]
  * [Цены на виртуальные машины Linux]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Размеры виртуальных машин Windows в Azure]: https://docs.microsoft.com/azure/virtual-machines/sizes
[Размеры виртуальных машин Linux в Azure]: https://docs.microsoft.com/azure/virtual-machines/sizes
[Цены на виртуальные машины Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Цены на виртуальные машины Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: media/managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: media/managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: media/managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: media/managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: media/managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: media/managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: media/managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: media/managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: media/managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: media/managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: media/managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: media/managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: media/managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: media/managing-virtual-machines-using-azure-explorer/CR08.png
