---
title: Расширение псевдонимов Azure CLI
description: Как использовать расширение псевдонимов Azure CLI
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 4d324235360e017970a215226572e3effeb9b917
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030856"
---
# <a name="the-azure-cli-alias-extension"></a>Расширение псевдонимов Azure CLI

Расширение псевдонимов позволяет пользователям определять команды для Azure CLI с помощью существующих команд. Псевдонимы упрощают рабочий процесс за счет использования ярлыков. Так как поддержка псевдонимов осуществляется на базе модуля шаблонов Jinja2, вы можете использовать расширенные функции обработки аргументов.

> [!NOTE]
> Эта функция предоставляется в режиме общедоступной предварительной версии. Функции и формат файла конфигурации могут отличаться.

## <a name="install-the-alias-extension"></a>Установка расширения псевдонимов

Минимальная требуемая версия Azure CLI для использования расширения псевдонимов — **2.0.28**. Чтобы проверить используемую версию CLI, выполните `az --version`. Если необходимо обновить установленный пакет CLI, следуйте инструкциям в статье [Установка Azure CLI](./install-azure-cli.md).

Установите расширение с помощью команды [az extension add](/cli/azure/extension#az-extension-add).

```azurecli-interactive
az extension add --name alias
```

Проверьте установку расширения с помощью команды [az extension list](/cli/azure/extension#az-extension-list). Если расширение псевдонимов установлено правильно, оно отобразится в выходных данных команды.

```azurecli-interactive
az extension list --output table --query '[].{Name:name}'
```

```output
Name
------
alias
```

## <a name="keep-the-extension-up-to-date"></a>Обновление расширения

Так как расширение псевдонимов сейчас активно разрабатывается, новые версии выходят регулярно. При обновлении CLI новые версии не устанавливаются. Установите обновление расширения с помощью команды [az extension update](/cli/azure/extension#az-extension-update).

```azurecli-interactive
az extension update --name alias
```

## <a name="manage-aliases-for-the-azure-cli"></a>Управление псевдонимами для Azure CLI

Расширение псевдонимов позволяет создавать псевдонимы для других команд CLI и управлять ими. Чтобы просмотреть сведения о всех доступных командах и их параметрах, выполните команду alias с параметром `--help`.

```azurecli-interactive
az alias --help
```

## <a name="create-simple-alias-commands"></a>Создание простых псевдонимов команд

Псевдонимы используются для сокращения существующих групп команд или имен команд. Например, можно сократить группу команд `group` до `rg` и команду `list` до `ls`.

```azurecli-interactive
az alias create --name rg --command group
az alias create --name ls --command list
```

Эти заново определенные псевдонимы теперь можно использовать в любом месте согласно их определению.

```azurecli-interactive
az rg list
az rg ls
az vm ls
```

Не добавляйте `az` в команду.

Псевдонимы также могут быть представлены сочетаниями клавиш для завершения команд. В следующем примере перечислены доступные группы ресурсов и их расположения в выходной таблице:

```azurecli-interactive
az alias create --name ls-groups --command "group list --query '[].{Name:name, Location:location}' --output table"
```

Теперь `ls-groups` можно запускать, как любые другие команды CLI.

```azurecli-interactive
az ls-groups
```

## <a name="create-an-alias-command-with-arguments"></a>Создание псевдонима команды с аргументами

Вы также можете добавить позиционные аргументы для псевдонима команды, включив их как `{{ arg_name }}` в имя псевдонима. Пробелы внутри скобок являются обязательными.

```azurecli-interactive
az alias create --name "alias_name {{ arg1 }} {{ arg2 }} ..." --command "invoke_including_args"
```

В следующем примере псевдонима показано, как использовать позиционные аргументы, чтобы получить общедоступный IP-адрес для виртуальной машины.

```azurecli-interactive
az alias create \
    --name "get-vm-ip {{ resourceGroup }} {{ vmName }}" \
    --command "vm list-ip-addresses --resource-group {{ resourceGroup }} --name {{ vmName }}
        --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress"
```

При выполнении этой команды вы предоставляете значения позиционным аргументам.

```azurecli-interactive
az get-vm-ip MyResourceGroup MyVM
```

В командах-псевдонимах также можно использовать переменные среды, которые вычисляются во время выполнения. В следующем примере добавляется псевдоним `create-rg`, который создает группу ресурсов в `eastus` и добавляет тег `owner`. Для этого тега присваивается значение локальной переменной `USER`.

```azurecli-interactive
az alias create \
    --name "create-rg {{ groupName }}" \
    --command "group create --name {{ groupName }} --location eastus --tags owner=\$USER"
```

Чтобы зарегистрировать переменные среды внутри команды псевдонима, знак доллара `$` необходимо экранировать.

## <a name="process-arguments-using-jinja2-templates"></a>Обработка аргументов с помощью шаблонов Jinja2

Подстановка аргументов в расширении псевдонимов выполняется с помощью шаблона [Jinja2](http://jinja.pocoo.org/docs/2.10/). Шаблоны Jinja2 позволяют манипулировать аргументами.

С помощью шаблонов Jinja2 вы можете создавать псевдонимы, которые принимают другие типы аргументов, отличные от аргументов базовой команды. Например, можно создать псевдоним, который принимает URL-адрес хранилища. Затем этот URL-адрес анализируется для передачи имен учетной записи и контейнера команде хранилища.

```azurecli-interactive
az alias create \
    --name 'storage-ls {{ url }}' \
    --command "storage blob list
        --account-name {{ url.replace('https://', '').split('.')[0] }}
        --container-name {{ url.replace('https://', '').split('/')[1] }}"
```

См. дополнительные сведения о [модуле шаблонов Jinja2](http://jinja.pocoo.org/docs/2.10/templates/).

## <a name="alias-configuration-file"></a>Файл конфигурации псевдонимов

Еще один способ создания и изменения псевдонимов заключается в изменении файла конфигурации псевдонимов. Определения псевдонимов команд записываются в файл конфигурации, расположенный здесь: `$AZURE_USER_CONFIG/alias`. По умолчанию для `AZURE_USER_CONFIG` устанавливается значение `$HOME/.azure` в Linux и macOS и `%USERPROFILE%\.azure` в Windows. Файлы конфигурации псевдонимов записываются в формате INI. Псевдонимы команд имеют такой формат:

```ini
[alias_name]
command = invoked_commands
```

Псевдонимы с позиционными аргументами имеют такой формат:

```ini
[alias_name {{ arg1 }} {{ arg2 }} ...]
command = invoked_commands_including_args
```

## <a name="create-an-alias-command-with-arguments-via-the-alias-configuration-file"></a>Создание псевдонима команды с аргументами с помощью файла конфигурации псевдонимов

В следующем примере показан псевдоним команды с аргументами. Эта команда получает общедоступный IP-адрес виртуальной машины. Команды-псевдонимы должны занимать одну строку и использовать все аргументы в имени псевдонима.

```ini
[get-vm-ip {{ resourceGroup }} {{ vmName }}]
command = vm list-ip-addresses --resource-group {{ resourceGroup }} --name {{ vmName }} --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress
```

## <a name="uninstall-the-alias-extension"></a>Удаление расширения псевдонимов

Удалите расширение с помощью команды [az extension remove](/cli/azure/extension#az-extension-remove).

```azurecli-interactive
az extension remove --name alias
```

Если вы удалили расширение из-за ошибки или другой проблемы, [отправьте сообщение об этом на сайте GitHub](https://github.com/Azure/azure-cli-extensions/issues), чтобы мы реализовали исправление.
