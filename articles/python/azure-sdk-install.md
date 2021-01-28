---
title: Установка пакетов библиотеки Azure SDK для Python
description: Инструкции по установке, удалению и проверке пакета Azure SDK или библиотек Python с помощью pip. Эта статься содержит сведения об установке конкретных версий и предварительных версий пакетов.
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 109631849b94f79530c93a8984813c424968f199
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759555"
---
# <a name="how-to-install-azure-library-packages-for-python"></a>Описание установки пакетов библиотеки Azure для Python

Пакет Azure SDK для Python состоит из множества отдельных библиотек, перечисленных на странице [индекса пакета](azure-sdk-library-package-index.md). Пакеты библиотек, необходимые для проекта, устанавливаются с помощью `pip install`.

С помощью этих библиотек можно подготавливать ресурсы и управлять ими в службах Azure (с помощью библиотек управления, которые содержат `-mgmt` в своих именах) и подключаться к этим ресурсам из кода приложения (с помощью клиентских библиотек).

## <a name="install-the-latest-version-of-a-library"></a>Установка последней версии библиотеки

```cmd
pip install azure-storage-blob
```

```cmd
pip install azure-mgmt-storage
```

`pip install` получает последнюю версию библиотеки в текущей среде Python.

В Linux-системах библиотеку необходимо устанавливать для каждого пользователя отдельно. Установка библиотек для всех пользователей с `sudo pip install` не поддерживается.

## <a name="install-specific-library-versions"></a>Установка конкретных версий библиотеки

```cmd
pip install azure-storage-blob==12.0.0
```

```cmd
pip install azure-mgmt-storage==10.0.0
```

Укажите нужную версию в командной строке, выполнив `pip install`.

## <a name="install-preview-packages"></a>Установка пакетов в предварительной версии

```cmd
pip install --pre azure-storage-blob
```

```cmd
pip install --pre azure-mgmt-storage
```

Чтобы установить последнюю предварительную версию библиотеки, включите в командную строку флаг `--pre`.

Корпорация Майкрософт периодически выпускает предварительные версии пакетов библиотеки с поддержкой новых функций и предупреждает, что библиотека может измениться и не должна использоваться в проектах для рабочей среды.

## <a name="verify-a-library-installation"></a>Проверка установки библиотеки

```cmd
pip show azure-storage-blob
```

```cmd
pip show azure-mgmt-storage
```

Чтобы проверить, установлена ли библиотека, используйте команду `pip show <library>`. Если библиотека установлена, после выполнения команды отобразятся сведения о версии и другие сводные данные. Если нет, ничего не отобразится.

Вы также можете использовать `pip freeze` или `pip list`, чтобы просмотреть все библиотеки, установленные в текущей среде Python.

## <a name="uninstall-a-library"></a>Удаление библиотеки

```cmd
pip uninstall azure-storage-blob
```

Чтобы удалить библиотеку, используйте команду `pip uninstall <library>`.
