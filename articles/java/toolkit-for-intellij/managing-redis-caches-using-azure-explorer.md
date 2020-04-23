---
title: Управление кэшами Redis с помощью Azure Explorer для IntelliJ
description: Узнайте, как управлять кэшами Redis Azure с помощью Azure Explorer для IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 2a059fa72207e8dde47eda7020ced90bd56b75a4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670160"
---
# <a name="managing-redis-caches-using-the-azure-explorer-for-intellij"></a>Управление кэшами Redis с помощью Azure Explorer для IntelliJ

Azure Explorer, входящий в состав набора средств Azure для IntelliJ, предоставляет разработчикам на Java удобное решение для управления кэшами Redis в их учетной записи Azure из интегрированной среды разработки IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-intellij"></a>Создание кэша Redis с помощью IntelliJ

Приведенные ниже пошаговые инструкции помогут вам создать кэш Redis с использованием Azure Explorer.

1. Войдите в свою учетную запись Azure, следуя инструкциям из статьи [Инструкции по входу для набора средств Azure для IntelliJ].

1. В окне средства **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Кэши Redis** и выберите пункт **Create Redis Cache** (Создать кэш Redis).

   ![Меню создания кэша Redis][CR01]

1. Когда отобразится диалоговое окно **Новый кэш Redis**, укажите значения для следующих параметров:

   ![Диалоговое окно New Redis Cache (Новый кэш Redis)][CR02]

   а. **DNS-имя**. Определяет поддомен DNS для нового кэша Redis, который добавляется перед адресом redis.cache.windows.net, например *wingtiptoys.redis.cache.windows.net*.

   b. **Подписка**: Определяет подписку Azure, которую нужно использовать для нового кэша Redis.

   c. **Группа ресурсов**. Указывает группу ресурсов для кэша Redis. Нужно выбрать один из следующих параметров: 
      * **Создать**. Определяет, что нужно создать группу ресурсов. 
      * **Использовать существующую**. Указывает, что будет выбрана группа ресурсов, связанная с учетной записью Azure. 

   d. **Расположение.** Определяет расположение, в котором создается кэш Redis (например *Западная часть США*).

   д) **Ценовая категория**. Указывает ценовую категорию для кэша Redis. Этот параметр определяет количество клиентских подключений. (Дополнительные сведения см. на [странице с ценами на кэш Redis].)

   е) **Порт без SSL**. Указывает, разрешает ли кэш Redis подключения без использования SSL. По умолчанию разрешены только SSL-подключения.

1. Когда вы введете значения для всех параметров кэша Redis, нажмите кнопку **ОК**.

После создания кэш Redis отобразится в Azure Explorer.

   ![Кэш Redis в Azure Explorer][CR03]

> [!NOTE]
>
> Дополнительные сведения о настройке кэша Redis для Azure см. в статье [Настройка кэша Redis для Azure].
>

## <a name="display-the-properties-for-your-redis-cache-in-intellij"></a>Отображение свойств для кэша Redis в IntelliJ

1. В Azure Explorer щелкните правой кнопкой мыши кэш Redis и выберите пункт **Показать свойства**.

   ![Контекстное меню Azure Explorer для отображения свойств кэша Redis][SP01]

1. В Azure Explorer отобразятся свойства кэша Redis.

   ![Свойства кэша Redis][SP02]

## <a name="delete-your-redis-cache-by-using-intellij"></a>Удаление кэша Redis с помощью IntelliJ

1. В Azure Explorer щелкните правой кнопкой мыши кэш Redis и выберите пункт **Удалить**.

   ![Контекстное меню Azure Explorer для удаления кэша Redis][DE01]

1. При появлении запроса выберите **Да**, чтобы удалить кэш Redis.

   ![Запрос на удаление кэша Redis][DE02]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о кэшах Redis для Azure, параметрах конфигурации и ценах см. по следующим ссылкам:

* [кэш Azure Redis]
* [Документация по Кэшу Redis]
* [странице с ценами на кэш Redis]
* [Настройка кэша Redis для Azure]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[странице с ценами на кэш Redis]: https://azure.microsoft.com/pricing/details/cache/
[кэш Azure Redis]: https://azure.microsoft.com/services/cache/
[Документация по Кэшу Redis]: /azure/redis-cache
[Настройка кэша Redis для Azure]: /azure/redis-cache/cache-configure
[Инструкции по входу для набора средств Azure для IntelliJ]: ./sign-in-instructions.md

<!-- IMG List -->

[CR01]: media/managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: media/managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: media/managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: media/managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: media/managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: media/managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: media/managing-redis-caches-using-azure-explorer/DE02.png
