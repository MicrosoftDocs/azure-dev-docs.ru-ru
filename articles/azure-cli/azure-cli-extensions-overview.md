---
title: Расширения Azure CLI
description: Использование расширений с Azure CLI
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 73488fc464ed052f22f071f6373fb6b8f682b778
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030986"
---
# <a name="use-extensions-with-azure-cli"></a>Использование расширений с Azure CLI 

В Azure CLI можно загружать расширения. Расширения — это пакеты Python wheel, которые не поставляются вместе с CLI, но выполняются в виде команд CLI.
Расширения позволяют получить доступ к экспериментальным командам и предварительным выпускам команд. Кроме того, расширения позволяют создавать собственные интерфейсы командной строки. В этой статье описано, как управлять расширениями, и описаны распространенные сценарии их использования.

## <a name="find-extensions"></a>Поиск расширений

Список расширений, которые предоставляет и поддерживает Майкрософт, можно просмотреть с помощью команды [az extension list-available](/cli/azure/extension#az-extension-list-available).

```azurecli-interactive
az extension list-available --output table
```

[Список расширений](azure-cli-extensions-list.md) также можно найти на веб-сайте с документацией.

## <a name="install-extensions"></a>Установка расширений

Когда вы найдете расширение для установки, используйте команду [az extension add](https://docs.microsoft.com/cli/azure/extension#az-extension-add), чтобы получить его. Если расширение включено в список в `az extension list-available`, его можно установить по имени.

```azurecli-interactive
az extension add --name <extension-name>
```

Если требуется расширение из внешнего ресурса или у вас есть прямая ссылка на него, можно указать URL-адрес источника или локальный путь. Расширение _должно_ представлять собой скомпилированный файл Python wheel.

```azurecli-interactive
az extension add --source <URL-or-path>
```

Установленное расширение можно найти в папке, указанной в переменной среды `$AZURE_EXTENSION_DIR`. Если эта переменная не задана, в Linux и macOS по умолчанию устанавливается значение `$HOME/.azure/cliextensions`, а в Windows — `%USERPROFILE%\.azure\cliextensions`.

## <a name="update-extensions"></a>Обновление расширений

Если расширение установлено с указанием имени, обновите его с помощью команды [az extension update](https://docs.microsoft.com/cli/azure/extension#az-extension-update).

```azurecli-interactive
az extension update --name <extension-name>
```

В противном случае расширение можно обновить из источника, выполнив инструкции по [установке расширений](#install-extensions).

Если имя расширения по какой-либо причине не может быть разрешено CLI, удалите это расширение и установите повторно. Расширение также может стать элементом основного пакета CLI.
Попробуйте обновить CLI, как описано в руководстве по [установке Azure CLI](install-azure-cli.md), и проверьте, добавлены ли команды расширения.

## <a name="uninstall-extensions"></a>Удаление расширений

Если расширение больше не нужно, удалите его с помощью команды [az extension remove](https://docs.microsoft.com/cli/azure/extension#az-extension-remove).

```azurecli-interactive
az extension remove --name <extension-name>
```

Также расширение можно вручную удалить из расположения, в котором оно установлено. Переменная среды `$AZURE_EXTENSION_DIR` определяет путь установки модулей.
Если эта переменная не задана, в Linux и macOS по умолчанию устанавливается значение `$HOME/.azure/cliextensions`, а в Windows — `%USERPROFILE%\.azure\cliextensions`.

```bash
rm -rf $AZURE_EXTENSION_DIR/<extension-name>
```

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

Ниже приведены ответы на некоторые часто задаваемые вопросы о расширениях CLI.

### <a name="what-file-formats-are-allowed-for-installation"></a>Какие форматы файлов разрешены для установки?

Сейчас в качестве расширений можно устанавливать только wheel-файлы Python.

### <a name="can-extensions-replace-existing-commands"></a>Могут ли расширения заменить существующие команды?

Да. Расширения могут заменить существующие команды. Но перед выполнением замененной команды в CLI отобразится предупреждение.

### <a name="how-can-i-tell-if-an-extension-is-in-pre-release"></a>Как понять, что расширение выпущено в предварительной версии?

В документации по расширению и формате версии будет указано, является ли оно предварительным выпуском. Майкрософт часто выпускает предварительные версии команд в виде расширений CLI с возможностью последующего включения в основной продукт CLI. Когда команды больше не являются расширениями, старое расширение следует удалить. 

### <a name="can-extensions-depend-upon-each-other"></a>Могут ли расширения зависеть друг от друга?

Нет. Так как CLI не гарантирует порядок загрузки, зависимости могут быть не удовлетворены. Удаление одного расширения не влияет на другие.

### <a name="are-extensions-updated-along-with-the-cli"></a>Обновляются ли расширения вместе с CLI?

Нет. Расширения следует обновлять отдельно, как описано в разделе [Обновление расширений](#update-extensions).

### <a name="how-to-develop-our-own-extension"></a>Разработка собственного расширения
См. сведения в официальном репозитории. [Azure/azure-cli-extensions](https://github.com/Azure/azure-cli/tree/master/doc/extensions)
