---
title: Установка пакета Azure SDK для Python
description: Сведения об установке пакета Azure SDK для Python с помощью PIP или GitHub. Пакет SDK для Azure можно установить в виде отдельных библиотек или как полный пакет.
author: kraigb
ms.author: kraigb
manager: barbkess
ms.service: multiple
ms.date: 10/31/2019
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: c2a8b2da8cdb0d55bad260ed1f1f6e5ee2b8efc5
ms.sourcegitcommit: 7e5392a0af419c650225cfaa10215d1e0e56ce71
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73568193"
---
# <a name="install-the-azure-sdk-for-python"></a>Установка пакета Azure SDK для Python

Пакет Azure SDK для Python предоставляет API, с помощью которого можно взаимодействовать с Azure из кода Python. Вы можете установить отдельные библиотеки из пакета SDK в зависимости от ваших потребностей или полный набор библиотек вместе.

Версии CPython 2,7 и 3.5.3+, а также с PyPy 5.4+ поддерживают и тестируют пакет Azure SDK для Python. Разработчики также используют пакет SDK с другими интерпретаторами, такими как IronPython и Jython, однако при этом вы можете столкнуться с отдельными проблемами и несовместимостью. При необходимости, последнюю версию интерпретатора Python можно установить из [python.org/downloads](https://www.python.org/downloads).

## <a name="install-sdk-libraries-using-pip"></a>Установка библиотек SDK с помощью PIP

Пакет Azure SDK для Python состоит из нескольких отдельных библиотек, каждая из которых подготавливает конкретные службы Azure или работает с ними. Вы можете установить каждую из них, используя `pip install <library>` с именами, указанными в [списке библиотеки пакета SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/packages.md). (Этот список содержит ссылки на полезные файлы сведений для каждой библиотеки.)

Например, при использовании Службы хранилища Azure, вы можете установить библиотеку `azure-storage-file`, `azure-storage-blob`или `azure-storage-queue`. Если вы используете таблицы Azure Cosmos DB, установите `azure-cosmosdb-table`. Функции Azure поддерживаются в библиотеке `azure-functions` и т. д. Эти библиотеки, начинающиеся с `azure-mgmt-`, предоставляют API для подготовки ресурсов Azure.

### <a name="install-specific-library-versions"></a>Установка конкретных версий библиотеки

Если необходимо установить определенную версию библиотеки, укажите ее версию в командной строке:

```bash
pip install azure-storage-blob==12.0.0
```

### <a name="install-preview-packages"></a>Установка пакетов в предварительной версии

Корпорация Майкрософт регулярно выпускает предварительные версии библиотек пакета SDK, которые поддерживают предстоящие функции. Чтобы установить последнюю предварительную версию библиотеки, включите в командную строку флаг `--pre`. 

```bash
# Install all preview versions of the Azure SDK for Python
pip install --pre azure

# Install the preview version for azure-storage-blob only.
pip install --pre azure-storage-blob
```

## <a name="verify-sdk-installation-details-with-pip"></a>Проверка сведений установки пакета SDK с помощью pip

Используйте команду `pip show <library>`, чтобы убедиться, что библиотека установлена. Если библиотека установлена, команда отображает версию и другие сводные данные. Если библиотека не установлена, при выполнении команды сведений не будет.

```bash
# Check installation of the Azure SDK for Python
pip show azure

# Check installation of a specific library
pip show azure-storage-blob
```

Вы также можете использовать `pip freeze` или `pip list`, чтобы просмотреть все библиотеки, установленные в текущей среде Python.

## <a name="uninstall-azure-sdk-for-python-libraries"></a>Удаление пакета Azure SDK для библиотек Python

Чтобы удалить отдельную библиотеку, используйте `pip uninstall <library>`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Подробнее об использовании пакета SDK](python-sdk-azure-get-started.yml)
