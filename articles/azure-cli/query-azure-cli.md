---
title: Запросы результатов команд в Azure CLI
description: Сведения о том, как выполнять запросы JMESPath к результатам команд Azure CLI.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: c9f0afdd626ac8d9af027db02275e5a553595473
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030746"
---
# <a name="query-azure-cli-command-output"></a>Запросы к выходным данным команд Azure CLI

Интерфейс Azure CLI использует аргумент `--query` для выполнения [запроса JMESPath](http://jmespath.org) к результатам выполнения команд. JMESPath — это язык запросов для JSON, который позволяет выбирать и изменять выходные данные команд CLI. Запросы выполняются к выходным данным в формате JSON до их форматирования для отображения.

Аргумент `--query` поддерживается всеми командами в Azure CLI. В этой статье описано использование возможностей JMESPath на основе ряда небольших простых примеров.

## <a name="dictionary-and-list-cli-results"></a>Результаты команд CLI в виде словарей и списков

Даже если используется формат выходных данных, отличный от JSON, при выполнении запросов результаты выполнения команд сначала обрабатываются как объекты JSON. Результаты выполнения команд CLI представляют собой массив или словарь JSON. Массивы — это последовательности объектов, доступ к которым может осуществляться по индексу, а словари — это неупорядоченные объекты, доступ к которым осуществляется с помощью ключей. Команды, которые _могут_ возвращать несколько объектов, возвращают массив, а команды, которые _всегда_ возвращают _только_ один объект, возвращают его в виде словаря.

## <a name="get-properties-in-a-dictionary"></a>Получение свойств из словаря

При работе с результатами в виде словаря вы всегда можете получить свойства верхнего уровня с помощью ключа. Для доступа к свойствам вложенных словарей используется символ `.` (__часть выражения__). Прежде чем перейти к запросам, рассмотрим неизмененные выходные данные команды `az vm show`:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM -o json
```

Результаты этой команда выводятся в виде словаря. Часть содержимого опущена.

```json
{
  "additionalCapabilities": null,
  "availabilitySet": null,
  "diagnosticsProfile": {
    "bootDiagnostics": {
      "enabled": true,
      "storageUri": "https://xxxxxx.blob.core.windows.net/"
    }
  },
  ...
  "osProfile": {
    "adminPassword": null,
    "adminUsername": "azureuser",
    "allowExtensionOperations": true,
    "computerName": "TestVM",
    "customData": null,
    "linuxConfiguration": {
      "disablePasswordAuthentication": true,
      "provisionVmAgent": true,
      "ssh": {
        "publicKeys": [
          {
            "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso",
            "path": "/home/azureuser/.ssh/authorized_keys"
          }
        ]
      }
    },
    "secrets": [],
    "windowsConfiguration": null
  },
  ....
}
```

Следующая команда предназначена для получения открытых ключей SSH, имеющих разрешение на подключение к виртуальной машине, с помощью запроса:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query osProfile.linuxConfiguration.ssh.publicKeys -o json
```

```json
[
  {
    "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso",
    "path": "/home/azureuser/.ssh/authorized_keys"
  }
]
```

## <a name="get-a-single-value"></a>Получение одного значения

Часто нужно, чтобы команда CLI должна вернуть только _одно_ значение, например идентификатор ресурса Azure, имя ресурса, имя пользователя или пароль. При этом значение должно быть сохранено в локальной переменной. Чтобы получить одно свойство, сначала убедитесь, что вам удалось извлечь только одно свойство из запроса. В последнем примере показано, как получить только имя пользователя администратора:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o json
```

```JSON
"azureuser"
```

Оно выглядит как допустимое отдельное значение, но обратите внимание, что символы `"` возвращаются как часть выходных данных. Это означает, что объект представляет собой строку JSON. Важно отметить, что при назначении этого значения переменной среды непосредственно в качестве выходных данных команды, кавычки могут __не__ интерпретироваться оболочкой:

```bash
USER=$(az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o json)
echo $USER
```

```output
"azureuser"
```

Это явно не то, что нужно. В этом случае вам нужно использовать формат выходных данных с возвращаемыми значениями с информацией о типах без кавычек. Наилучший вариант, который предоставляет CLI для этой цели, — `tsv` или значение с разделением знаками табуляции. В частности, при извлечении отдельного значения (не словаря или списка) выходные данные `tsv` гарантированно не будут включать кавычки.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o tsv
```

```output
azureuser
```

См. подробнее о [формате выходных данных `tsv`](format-output-azure-cli.md#tsv-output-format).

## <a name="get-multiple-values"></a>Получение нескольких значений

Чтобы получить несколько свойств, заключите список выражений, разделенных запятыми, в квадратные скобки — `[ ]` (__список из нескольких элементов__). Чтобы получить имя виртуальной машины, имя администратора и ключ SSH с помощью одной команды, введите ее в следующем виде:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '[name, osProfile.adminUsername, osProfile.linuxConfiguration.ssh.publicKeys[0].keyData]' -o json
```

```json
[
  "TestVM",
  "azureuser",
  "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso"
]
```

Эти значения включены в полученный массив в том порядке, в котором они были указаны в запросе. Поскольку результат имеет формат массива, значения не имеют соответствующих ключей.

## <a name="rename-properties-in-a-query"></a>Переименование свойств в запросе

Чтобы при запросе нескольких значений получить словарь вместо массива, используйте оператор `{ }` (__хэш-таблица из нескольких элементов__).
Формат запроса для получения хэш-таблицы имеет вид `{displayName:JMESPathExpression, ...}`.
`displayName` — это строка, отображаемая в выходных данных, а `JMESPathExpression` — вычисляемое выражение JMESPath. Изменим пример из предыдущего раздела, чтобы заменить список элементов хэш-таблицей:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '{VMName:name, admin:osProfile.adminUsername, sshKey:osProfile.linuxConfiguration.ssh.publicKeys[0].keyData }' -o json
```

```json
{
  "VMName": "TestVM",
  "admin": "azureuser",
  "ssh-key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso"
}
```

## <a name="get-properties-in-an-array"></a>Получение свойств из массива

Массив не имеет свойств как таковых, но доступ к нему может осуществляться по индексу. Эта возможность показана в предыдущем примере, когда выражение `publicKeys[0]` использовалось для получения элемента с индексом `publicKeys` из массива. Порядок выходных данных в командах CLI не гарантирован. Поэтому используйте индексы, только если вы уверены в порядке выходных данных или вам все равно, какой элемент вы получите. Чтобы получить доступ к элементам массива, используется одна из двух операций: _преобразование в плоскую структуру_ или _фильтрация_. В этом разделе описано преобразование массива в плоскую структуру.

Преобразование массива в плоскую структуру выполняется с помощью оператора JMESPath `[]`. Все выражения после оператора `[]` применяются к каждому элементу в текущем массиве.
Если оператор `[]` стоит в начале запроса, он преобразовывает в плоскую структуру результат выполнения команды CLI. Эта возможность позволяет изучить результат выполнения команды `az vm list`.
Чтобы получить имя, сведения об операционной системе и имя администратора для каждой виртуальной машины в группе ресурсов, выполните следующую команду:

```azurecli-interactive
az vm list -g QueryDemo --query '[].{Name:name, OS:storageProfile.osDisk.osType, admin:osProfile.adminUsername}' -o json
```

```json
[
  {
    "Name": "Test-2",
    "OS": "Linux",
    "admin": "sttramer"
  },
  {
    "Name": "TestVM",
    "OS": "Linux",
    "admin": "azureuser"
  },
  {
    "Name": "WinTest",
    "OS": "Windows",
    "admin": "winadmin"
  }
]
```

При использовании формата выходных данных `--output table` имена столбцов соответствуют значению `displayKey` в хэш-таблице:

```azurecli-interactive
az vm list -g QueryDemo --query '[].{Name:name, OS:storageProfile.osDisk.osType, Admin:osProfile.adminUsername}' --output table
```

```output
Name     OS       Admin
-------  -------  ---------
Test-2   Linux    sttramer
TestVM   Linux    azureuser
WinTest  Windows  winadmin
```

> [!NOTE]
>
> Некоторые ключи отфильтровываются и не печатаются в табличном представлении. Эти ключи — `id`, `type` и `etag`. Чтобы отобразить эти значения, можно изменить имя ключа в хэш-таблице.
>
> ```azurecli-interactive
> az vm show -g QueryDemo -n TestVM --query "{objectID:id}" -o table
> ```

Преобразовать в плоскую структуру можно любой массив, а не только массив верхнего уровня, возвращаемый командой. В предыдущем разделе с помощью выражения `osProfile.linuxConfiguration.ssh.publicKeys[0].keyData` мы получили открытый ключ SSH, используемый для входа. Чтобы получить _все_ открытые ключи SSH, выражение можно записать в следующем виде: `osProfile.linuxConfiguration.ssh.publicKeys[].keyData`.
Этот запрос преобразовывает массив `osProfile.linuxConfiguration.ssh.publicKeys` в плоскую структуру, а затем применяет выражение `keyData` к каждому элементу:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '{VMName:name, admin:osProfile.adminUsername, sshKeys:osProfile.linuxConfiguration.ssh.publicKeys[].keyData }' -o json
```

```json
{
  "VMName": "TestVM",
  "admin": "azureuser",
  "sshKeys": [
    "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso\n"
  ]
}
```

## <a name="filter-arrays"></a>Фильтрация массивов

Еще одной операцией, используемой для получения данных из массива, является _фильтрация_. Фильтрация выполняется с помощью оператора JMESPath `[?...]`.
В этом операторе используется предикат. Предикат — это любое выражение, результат которого равен `true` или `false`. Выражения, предикат которых возвращает `true`, включаются в выходные данные.

JMESPath поддерживает стандартные операторы сравнения и логические операторы. Это операторы `<`, `<=`, `>`, `>=`, `==` и `!=`.
JMESPath также поддерживает логические операторы "И" (`&&`), "ИЛИ" (`||`) и "НЕ" (`!`). Выражения можно объединять в группы с помощью круглых скобок. Это позволяет использовать более сложные выражения в качестве предикатов. Подробные сведения о предикатах и логических операциях см. в [спецификации JMESPath](http://jmespath.org/specification.html).

В последнем примере мы преобразовали массив в плоскую структуру, чтобы получить список всех виртуальных машин в группе ресурсов. С помощью фильтров в эти выходные данные можно включить только виртуальные машины с ОС Linux:

```azurecli-interactive
az vm list -g QueryDemo --query "[?storageProfile.osDisk.osType=='Linux'].{Name:name,  admin:osProfile.adminUsername}" --output table
```

```output
Name    Admin
------  ---------
Test-2  sttramer
TestVM  azureuser
```

> [!IMPORTANT]
>
> В JMESPath строки всегда заключаются в одиночные кавычки (`'`). Если в строке, стоящей в предикате фильтра, используются двойные кавычки, команда вернет пустой результат.

В JMESPath также встроены функции, которые помогают фильтровать данные. Одна из таких функций — `contains(string, substring)`. Она позволяет проверить, содержит ли строка указанную подстроку. Выражения вычисляются до вызова функции. Поэтому первым аргументом может быть полное выражение JMESPath. В следующем примере выполняется поиск всех машин, использующих диски SSD для размещения ОС:

```azurecli-interactive
az vm list -g QueryDemo --query "[?contains(storageProfile.osDisk.managedDisk.storageAccountType,'SSD')].{Name:name, Storage:storageProfile.osDisk.managedDisk.storageAccountType}" -o json
```

```json
[
  {
    "Name": "TestVM",
    "Storage": "StandardSSD_LRS"
  },
  {
    "Name": "WinTest",
    "Storage": "StandardSSD_LRS"
  }
]
```

Этот запрос достаточно длинный. Ключ `storageProfile.osDisk.managedDisk.storageAccountType` используется дважды, и в выходных данных ему присвоено другое имя. Один из способов сократить запрос — применить фильтр после преобразования массива в плоскую структуру и выбора данных.

```azurecli-interactive
az vm list -g QueryDemo --query "[].{Name:name, Storage:storageProfile.osDisk.managedDisk.storageAccountType}[?contains(Storage,'SSD')]" -o json
```

```json
[
  {
    "Name": "TestVM",
    "Storage": "StandardSSD_LRS"
  },
  {
    "Name": "WinTest",
    "Storage": "StandardSSD_LRS"
  }
]
```

Если в массиве с множество элементов, возможно, применение фильтра перед выбором данных ускорит операцию.

Полный список встроенных функций см. в [этом разделе спецификации JMESPath](http://jmespath.org/specification.html#built-in-functions).

## <a name="change-output"></a>Изменение выходных данных

Функции JMESPath также можно использовать для обработки результатов запроса. Любая функция, которая возвращает значение, отличное от логического, изменяет результаты выражения.
Например, данные можно отсортировать по значению свойства с помощью функции `sort_by(array, &sort_expression)`. В JMESPath используется специальный оператор `&` для выражений, которые должны вычисляться позднее в функции. В следующем примере показано, как отсортировать список виртуальных машин по размеру диска с ОС:

```azurecli-interactive
az vm list -g QueryDemo --query "sort_by([].{Name:name, Size:storageProfile.osDisk.diskSizeGb}, &Size)" --output table
```

```output
Name     Size
-------  ------
TestVM   30
Test-2   32
WinTest  127
```

Полный список встроенных функций см. в [этом разделе спецификации JMESPath](http://jmespath.org/specification.html#built-in-functions).

## <a name="experiment-with-queries-interactively"></a>Интерактивное использование запросов

Чтобы начать изучение JMESPath, можно использовать пакет Python [JMESPath-terminal](https://github.com/jmespath/jmespath.terminal), который предоставляет интерактивную среду для работы с запросами. Данные подаются на вход, и в редакторе можно составлять и выполнять запросы.

```bash
pip install jmespath-terminal
az vm list --output json | jpterm
```
