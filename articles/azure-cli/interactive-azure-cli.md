---
title: Интерактивный режим Azure CLI
description: Использование Azure CLI в интерактивном режиме.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/09/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 7b3ee1e284e7f771c661bb65bf8b8ab53dafd77f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82031176"
---
# <a name="azure-cli-interactive-mode"></a>Интерактивный режим Azure CLI

Azure CLI можно использовать в интерактивном режиме, выполнив команду `az interactive`.
В этом режиме открывается интерактивная оболочка с функцией автозавершения, описаниями команд и примерами.

![Интерактивный режим](./media/interactive-azure-cli/webapp-create.png)

> [!NOTE]
> Здесь мы не используем стиль по умолчанию, который неудобно читать, как и черный фон.

Если вы еще не вошли со своей учетной записью, выполните команду `login`.

## <a name="configure"></a>Configure

Интерактивный режим при необходимости позволяет отобразить описание команд, описание параметров и примеры команд.
Отображение описаний и примеров можно включить или отключить с помощью `F1`.

![Описания и примеры](./media/interactive-azure-cli/descriptions-and-examples.png)

Можно включить или отключить отображение значений параметров по умолчанию с помощью `F2`.

![Значения по умолчанию](./media/interactive-azure-cli/defaults.png)

`F3` переключает отображение некоторых ключевых жестов.

![Жесты](./media/interactive-azure-cli/gestures.png)

## <a name="scope"></a>Область

Вы можете задать область для интерактивного режима, указав определенную группу команд, например `vm` или `vm image`.
При этом будут интерпретироваться все команды в этой области.
Это заметно упрощает работу, если для нее вам требуется определенная группа команд.

Например, требуется ввести следующие команды.

```azurecli
az>> vm create -n myVM -g myRG --image UbuntuLTS
az>> vm list -o table
```

Вместо этого можно задать область для группы команд vm и ввести приведенные ниже команды.

```azurecli
az>> %%vm
az vm>> create -n myVM -g myRG --image UbuntuLTS
az vm>>list -o table
```

Кроме того, в качестве области можно задать группы команд более низкого уровня.
Для `vm image` можно задать область `%%vm image`.
В этом случае, так как мы уже задали `vm` в качестве области, можно было бы использовать `%%image`.

```azurecli
az vm>> %%image
az vm image>>
```

На этом этапе можно указать область `vm`, введя `%%..`, или указать корневой каталог, введя `%%`.

```azurecli
az vm image>> %%
az>>
```

## <a name="query"></a>Запрос

Чтобы выполнить запрос JMESPath к результатам последней выполненной команды, укажите `??` перед запросом JMESPath.
Например после создания группы можно получить идентификатор новой группы.

```azurecli
az>> group create -n myRG -l westEurope
az>> "?? id"
```

С помощью этого синтаксиса также можно использовать результат предыдущей команды в качестве аргумента для следующей команды.* Например, после перечисления всех групп можно перечислить все ресурсы типа `virtualMachine` в первой группе, расположение (location) которых — westeurope. 

```azurecli
az>> vm create --name myVM --resource-group myRG --image UbuntuLTS --no-wait -o json
az>> group list -o json
az>> resource list -g "?? [?location=='westeurope'].name | [0]" --query "[?type=='Microsoft.Compute/virtualMachines'].name
```

Чтобы узнать больше о выполнении запросов к результатам команд в Azure CLI, ознакомьтесь с [этой статьей](query-azure-cli.md).

## <a name="bash-commands"></a>Команды Bash

Можно выполнять команды оболочки, не выходя из интерактивного режима, с помощью `#[cmd]`.

```azurecli
az>> #dir
```

## <a name="examples"></a>Примеры

Некоторые команды имеют множество примеров.
Вы можете прокрутить экран до следующей странице примеров с помощью `CTRL-N`, а до предыдущей страницы — с помощью `CTRL-Y`.

![примеры](./media/interactive-azure-cli/examples.png)

Можно также изучить конкретный пример с помощью `::#`.

```azurecli
az>> vm create ::8
```
