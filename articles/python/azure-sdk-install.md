---
title: Установка пакета Azure SDK для библиотек Python
description: Инструкции по установке, удалению и проверке пакета Azure SDK или библиотек Python с помощью pip. Эта статься содержит сведения об установке конкретных версий и предварительных версий пакетов.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 302d17211480f9c8793d7be1de20a6ab5dec3c95
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2020
ms.locfileid: "83403664"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Установка пакета Azure SDK для библиотек Python

Пакет Azure SDK для Python предоставляет API, с помощью которого можно взаимодействовать с Azure из кода Python. Имена всех текущих библиотек можно найти на [странице индекса пакета Azure SDK для Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

Библиотеки, имена которых начинаются с `azure-mgmt`, — это библиотеки *управления*, используемые для подготовки и администрирования ресурсов Azure таким же способом, как с помощью [портала Azure](https://portal.azure.com) или [Azure CLI](/cli/azure/install-azure-cli). Например, для подготовки и администрирования ресурсов службы хранилища Azure применяется библиотека `azure-mgmt-storage`.

Все остальные библиотеки в пакете SDK — это *клиентские библиотеки*, которые используются из кода приложения для работы с подготовленными ресурсами. Например, для работы с BLOB-объектами службы хранилища Azure из кода приложения используется библиотека `azure-storage-blob`.

## <a name="install-the-latest-version-of-a-library"></a>Установка последней версии библиотеки

```bash
pip install azure-storage-blob
```

`pip install` устанавливает последнюю версию библиотеки в текущей среде Python.

В системах Linux пакет SDK не поддерживает установку библиотеки с помощью `sudo pip install` для всех пользователей. Каждому пользователю следует использовать `pip install` отдельно.

## <a name="install-specific-library-versions"></a>Установка конкретных версий библиотеки

```bash
pip install azure-storage-blob==12.0.0
```

Укажите версию в командной строке, выполнив `pip install`.

## <a name="install-preview-packages"></a>Установка пакетов в предварительной версии

```bash
pip install --pre azure-storage-blob
```

Чтобы установить последнюю предварительную версию библиотеки, включите в командную строку флаг `--pre`.

Корпорация Майкрософт регулярно выпускает предварительные версии библиотек пакета SDK с поддержкой новых функций и предупреждает, что библиотека может измениться и не должна использоваться в проектах для рабочей среды.

## <a name="verify-a-library-installation"></a>Проверка установки библиотеки

```bash
pip show azure-storage-blob
```

Чтобы проверить, установлена ли библиотека, используйте команду `pip show <library>`. Если библиотека установлена, после выполнения команды отобразятся сведения о версии и другие сводные данные. Если нет, ничего не отобразится.

Вы также можете использовать `pip freeze` или `pip list`, чтобы просмотреть все библиотеки, установленные в текущей среде Python.

## <a name="uninstall-a-library"></a>Удаление библиотеки

```bash
pip uninstall azure-storage-blob
```

Чтобы удалить библиотеку, используйте команду `pip uninstall <library>`.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы полностью готовы к написанию и запуску кода. Это можно сделать с помощью одного из следующих примеров:

> [!div class="nextstepaction"]
> [Пример. Создание группы ресурсов >>>](azure-sdk-example-resource-group.md)
