---
title: Форматы выходных данных для Azure CLI
description: Сведения о том, как форматировать результаты выполнения команды Azure CLI в виде таблиц, списков или объектов JSON.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 7f28a5cc7acd7e5d41e1ab91424a9f360a25b702
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82031036"
---
# <a name="output-formats-for-azure-cli-commands"></a>Форматы выходных данных для команд Azure CLI

Azure CLI использует JSON в качестве формата выходных данных по умолчанию, но поддерживает и другие форматы.  Параметр `--output` (`--out` или `-o`) позволяет форматировать данные, выводимые CLI. Ниже описаны значения аргументов и их типы:

--output | Описание
---------|-------------------------------
`json`   | Строка в формате JSON. Это значение по умолчанию.
`jsonc`  | Выделенная цветом строка JSON.
`yaml`   | YAML, альтернативный JSON машиночитаемый формат.
`table`  | Таблица ASCII с ключами в качестве заголовков столбцов.
`tsv`    | Значения, разделенные табуляцией, без ключей.
`none`   | Нет выходных данных, кроме сообщений об ошибках и предупреждений.

## <a name="json-output-format"></a>Формат выходных данных JSON.

Следующий пример отображает список виртуальных машин в подписках в стандартном формате JSON.

```azurecli-interactive
az vm list --output json
```

Следующие выходные данные содержат некоторые поля, которые исключены для краткости, и замененные сведения для идентификации.

```json
[
  {
    "availabilitySet": null,
    "diagnosticsProfile": null,
    "hardwareProfile": {
      "vmSize": "Standard_DS1"
    },
    "id": "/subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/DemoVM010",
    "instanceView": null,
    "licenseType": null,
    "location": "westus",
    "name": "DemoVM010",
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/.../resourceGroups/demorg1/providers/Microsoft.Network/networkInterfaces/DemoVM010VMNic",
          "primary": null,
          "resourceGroup": "demorg1"
        }
      ]
    },
          ...
          ...
          ...
]
```

## <a name="yaml-output-format"></a>Формат выходных данных YAML

`yaml`Выходные данные отображаются в [YAML](http://yaml.org/), формате сериализации данных обычного текста. YAML легче для восприятия, чем JSON, и сопоставим с ним. Входные данные конфигурации некоторых приложений и команд CLI задаются в формате YAML, а не JSON.

```azurecli-interactive
az vm list --out yaml
```

Следующие выходные данные содержат некоторые поля, которые исключены для краткости, и замененные сведения для идентификации.

```yaml
- availabilitySet: null
  diagnosticsProfile: null
  hardwareProfile:
    vmSize: Standard_DS1_v2
  id: /subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/DemoVM010
  identity: null
  instanceView: null
  licenseType: null
  location: westus
  name: ExampleVM1
  networkProfile:
    networkInterfaces:
    - id: /subscriptions/.../resourceGroups/DemoRG1/providers/Microsoft.Network/networkInterfaces/DemoVM010Nic
      primary: null
      resourceGroup: DemoRG1
  ...
...
```

## <a name="table-output-format"></a>Формат табличных выходных данных

Формат `table` выводит данные в виде таблицы ASCII, упрощая чтение и сканирование данных. Вложенные объекты не включаются в таблицу с выходными данными, но их можно отфильтровать как часть запроса. Некоторые поля не включаются в таблицу, поэтому этот формат лучше всего подходит, когда вам нужно быстро получить обзор данных в наглядной форме.

```azurecli-interactive
az vm list --out table
```

```output
Name         ResourceGroup    Location
-----------  ---------------  ----------
DemoVM010    DEMORG1          westus
demovm212    DEMORG1          westus
demovm213    DEMORG1          westus
KBDemo001VM  RGDEMO001        westus
KBDemo020    RGDEMO001        westus
```

Вы можете использовать параметр `--query` для настройки свойств и столбцов, которые будут отображаться в списке. Следующий пример показывает, как выбрать только имя виртуальной машины и имя группы ресурсов в команде `list`.

```azurecli-interactive
az vm list --query "[].{resource:resourceGroup, name:name}" -o table
```

```output
Resource    Name
----------  -----------
DEMORG1     DemoVM010
DEMORG1     demovm212
DEMORG1     demovm213
RGDEMO001   KBDemo001VM
RGDEMO001   KBDemo020
```

> [!NOTE]
> Некоторые ключи по умолчанию не отображаются в табличном представлении. Доступны следующие параметры: `id`, `type` и `etag`. Если вам нужно отобразить их в выходных данных, вы можете использовать функцию повторного добавления ключа JMESPath, чтобы изменить имя ключа и избежать фильтрации.
>
> ```azurecli-interactive
> az vm list --query "[].{objectID:id}" -o table
> ```

Дополнительные сведения об использовании запросов для фильтрации данных см. в [руководстве по использованию запросов JMESPath в Azure CLI](/cli/azure/query-azure-cli).

## <a name="tsv-output-format"></a>Формат выходных данных TSV

Формат выходных данных `tsv` возвращает значения, разделенные табуляцией и символом новой строки без дополнительного форматирования, ключей и других символов. Такой формат позволяет использовать выходные данные в других командах и инструментах, необходимых для обработки текста в определенной форме. Как и формат `table`, `tsv` не отображает вложенные объекты.

Если применить для предыдущего примера параметр `tsv`, отобразится результат в виде текста, разделенного табуляцией.

```azurecli-interactive
az vm list --out tsv
```

```output
None    None        /subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/DemoVM010    None    None    westus    DemoVM010            None    Succeeded    DEMORG1    None            Microsoft.Compute/virtualMachines    cbd56d9b-9340-44bc-a722-25f15b578444
None    None        /subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/demovm212    None    None    westus    demovm212            None    Succeeded    DEMORG1    None            Microsoft.Compute/virtualMachines    4bdac85d-c2f7-410f-9907-ca7921d930b4
None    None        /subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/demovm213    None    None    westus    demovm213            None    Succeeded    DEMORG1    None            Microsoft.Compute/virtualMachines    2131c664-221a-4b7f-9653-f6d542fbfa34
None    None        /subscriptions/.../resourceGroups/RGDEMO001/providers/Microsoft.Compute/virtualMachines/KBDemo001VM    None    None    westus    KBDemo001VM            None    Succeeded    RGDEMO001    None            Microsoft.Compute/virtualMachines    14e74761-c17e-4530-a7be-9e4ff06ea74b
None    None        /subscriptions/.../resourceGroups/RGDEMO001/providers/Microsoft.Compute/virtualMachines/KBDemo020   None    None    westus    KBDemo020            None    Succeeded    RGDEMO001    None            Microsoft.Compute/virtualMachines    36baa9-9b80-48a8-b4a9-854c7a858ece
```

Одним из ограничений формата вывода TSV является то, что порядок вывода не гарантируется. В интерфейсе командной строки порядок сохраняется за счет расположения ключей в алфавитном порядке в ответе JSON и последующем выводе их значений в том порядке, который соответствует выходным данным TSV. Это не гарантирует постоянного соблюдения порядка, так как формат ответа службы Azure может измениться.

Чтобы обеспечить требуемый порядок, необходимо использовать параметр `--query` и формат [списка из нескольких элементов](query-azure-cli.md#get-multiple-values). Если команда CLI возвращает один словарь JSON, используйте общий формат `[key1, key2, ..., keyN]` для обеспечения порядка ключей.  Если команда CLI возвращает массив, используйте общий формат `[].[key1, key2, ..., keyN]`, чтобы упорядочить значения столбцов.

Так, чтобы упорядочить приведенные выше сведения по идентификатору, расположению, группе ресурсов и имени виртуальной машины, сделайте следующее:

```azurecli-interactive
az vm list --out tsv --query '[].[id, location, resourceGroup, name]'
```

```output
/subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/DemoVM010    westus    DEMORG1    DemoVM010
/subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/demovm212    westus    DEMORG1    demovm212
/subscriptions/.../resourceGroups/DEMORG1/providers/Microsoft.Compute/virtualMachines/demovm213    westus    DEMORG1    demovm213
/subscriptions/.../resourceGroups/RGDEMO001/providers/Microsoft.Compute/virtualMachines/KBDemo001VM     westus  RGDEMO001       KBDemo001VM
/subscriptions/.../resourceGroups/RGDEMO001/providers/Microsoft.Compute/virtualMachines/KBDemo020       westus  RGDEMO001       KBDemo020
```

На следующем примере показано, как выходные данные команды `tsv` можно передать по каналу в другие команды оболочки bash. Фильтрация выходных данных и принудительное упорядочивание выполняются с помощью запроса. Команда `grep` выбирает элементы, содержащие текст RGD, а затем команда `cut` выбирает четвертое поле, чтобы отобразить имя виртуальной машины в выходных данных.

```azurecli-interactive
az vm list --out tsv --query '[].[id, location, resourceGroup, name]' | grep RGD | cut -f4
```

```output
KBDemo001VM
KBDemo020
```

## <a name="set-the-default-output-format"></a>Настройка формата вывода по умолчанию

Вы можете использовать интерактивную команду `az configure`, чтобы настроить окружение и выбрать параметры по умолчанию для форматов выходных данных. Формат выходных данных по умолчанию — `json`.

```azurecli-interactive
az configure
```

```output
Welcome to the Azure CLI! This command will guide you through logging in and setting some default values.

Your settings can be found at /home/defaultuser/.azure/config
Your current configuration is as follows:

  ...

Do you wish to change your settings? (y/N): y

What default output format would you like?
 [1] json - JSON formatted output that most closely matches API responses.
 [2] jsonc - Colored JSON formatted output that most closely matches API responses.
 [3] table - Human-readable output format.
 [4] tsv - Tab- and Newline-delimited. Great for GREP, AWK, etc.
 [5] yaml - YAML formatted output. An alternative to JSON. Great for configuration files.
 [6] none - No output, except for errors and warnings.
Please enter a choice [1]:
```

Дополнительные сведения о настройке среды см. в [руководстве по конфигурации Azure CLI](/cli/azure/azure-cli-configuration).
