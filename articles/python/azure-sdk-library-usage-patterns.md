---
title: Схемы использования библиотек Azure для Python
description: Общие сведения об использовании библиотек Azure SDK для Python
ms.date: 06/09/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: cf44dc4458014972b6c6e16a28acab164d8f0f89
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983325"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Библиотеки Azure для схем использования Python

Пакет SDK Azure для Python состоит из множества независимых библиотек, которые перечислены [здесь](azure-sdk-library-package-index.md).

У всех библиотек есть определенные общие характеристики и схемы использования, такие как установка и использование встроенного кода JSON для аргументов объекта.

## <a name="library-installation"></a>Установка библиотеки

Чтобы установить нужный пакет библиотеки, просто используйте `pip install`:

```cmd
# Install the management library for Azure Storage
pip install azure-mgmt-storage
```

```cmd
# Install the client library for Azure Storage
pip install azure-storage-blob
```

`pip install` получает последнюю версию библиотеки в текущей среде Python.

Можно также использовать команду `pip`, чтобы удалить библиотеку или установить определенную версию, включая предварительные версии. Дополнительные сведения см. в статье [Описание установки пакетов библиотеки Azure для Python](azure-sdk-install.md).

## <a name="asynchronous-operations"></a>Асинхронные операции

Многие операции, которые вызываются через объекты клиента и клиента управления (например, [`WebSiteManagementClient.web_apps.create_or_update`](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.operations.webappsoperations?view=azure-python#create-or-update-resource-group-name--name--site-envelope--custom-headers-none--raw-false--polling-true----operation-config-)), возвращают объект с типом `AzureOperationPoller[<type>]`, где `<type>` зависит от конкретной операции.

Тип возвращаемого значения [`AzureOperationPoller`](/python/api/msrestazure/msrestazure.azure_operation.azureoperationpoller?view=azure-python) означает, что операция является асинхронной. Соответственно, вам нужно вызвать метод `result` этого модуля опроса, чтобы дождаться фактического результата операции.

Следующий код взят из примера [ Подготовка и развертывание веб-приложения](azure-sdk-example-web-app.md), где показано, как использовать модуль опроса, чтобы дождаться результата.

```python
poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()
```

В нашем примере возвращаемое значение `create_or_update` имеет тип `AzureOperationPoller[Site]`, а значит возвращаемое значение `poller.result()` является объектом [Site](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.models.site?view=azure-python).

## <a name="exceptions"></a>Исключения

Как правило, библиотеки Azure генерируют исключения в тех случаях, когда операции не выполняются должным образом, в том числе при неудачных HTTP-запросах к REST API Azure. Это означает, что в коде приложения можно использовать блоки `try...except` вокруг операций библиотеки.

Дополнительные сведения о типах создаваемых исключений см. в документации по конкретной операции.

## <a name="logging"></a>Logging

В последних версиях библиотек Azure для создания выходных данных журнала используется стандартная библиотека Python `logging`. Вы можете задать уровень ведения журнала для отдельных библиотек, групп библиотек или всех библиотек сразу. После регистрации обработчика потока ведения журнала вы сможете включить ведение журнала для конкретного объекта клиента или конкретной операции. См. дополнительные сведения в статье [Ведение журнала в библиотеках Azure](azure-sdk-logging.md).

## <a name="proxy-configuration"></a>Конфигурация прокси-сервера

Чтобы указать прокси-сервер, можно использовать переменные среды или необязательные аргументы. См. дополнительные сведения в статье [Как конфигурировать прокси-серверы](azure-sdk-configure-proxy.md).

## <a name="optional-arguments-for-client-objects-and-methods"></a>Необязательные аргументы для объектов и методов клиента

В справочной документации по библиотеке аргумент `**kwargs` или `**operation_config**` часто встречается в сигнатурах конструктора объекта клиента или конкретного метода операции. Эти заполнители указывают, что соответствующий объект или метод могут поддерживать дополнительные именованные аргументы. Как правило, справочная документация описывает конкретные аргументы, которые можно использовать. Есть также несколько общих аргументов, которые поддерживаются во многих случаях, как описано в следующих разделах.

### <a name="arguments-for-libraries-based-on-azurecore"></a>Аргументы для библиотек на основе azure.core

Эти аргументы применяются к библиотекам, перечисленным на странице [Python - New Libraries](https://azure.github.io/azure-sdk/releases/latest/#python) (Python — Новые библиотеки).

| Имя                       | Тип | По умолчанию     | Описание |
| ---                        | ---  | ---         | ---         |
| logging_enable             | bool | False       | Включает ведение журнала. См. дополнительные сведения в статье [Ведение журнала в библиотеках Azure](azure-sdk-logging.md). |
| proxies                    | dict | {}          | URL-адреса прокси-серверов. См. дополнительные сведения в статье [Как конфигурировать прокси-серверы](azure-sdk-configure-proxy.md). |
| use_env_settings           | bool | True        | Если указано значение True, разрешено использовать переменные среды `HTTP_PROXY` и `HTTPS_PROXY` для прокси-серверов. Если указано значение False, переменные среды игнорируются. См. дополнительные сведения в статье [Как конфигурировать прокси-серверы](azure-sdk-configure-proxy.md). |
| connection_timeout         | INT  | 300         | Время ожидания в секундах для установки подключения к конечным точкам REST API Azure. |
| read_timeout               | INT  | 300         | Время ожидания в секундах для завершения операции REST API Azure (т. е. ожидания ответа). |
| retry_total                | INT  | 10          | Число допустимых повторных попыток при вызовах REST API. Укажите `retry_total=0`, чтобы отключить повторные попытки. |
| retry_mode                 | enum | exponential | Выбор линейной или экспоненциальной задержки перед выполнением повторных попыток. Если указано значение single, повторы выполняются через регулярные интервалы. Если указано значение exponential, каждая следующая повторная попытка выполняется с удвоенным временем ожидания. |

Отдельные библиотеки могут не поддерживать эти аргументы, поэтому для получения точных сведений обязательно проверьте справочную документацию по каждой конкретной библиотеке.

### <a name="arguments-for-non-core-libraries"></a>Аргументы для библиотек, не входящих в основной набор

| Имя               | Тип | По умолчанию | Описание |
| ---                | ---  | ---     | ---         |
| проверка             | bool | True    | Проверка SSL-сертификата. |
| cert               | str  | None    | Путь к локальному сертификату для проверки на стороне клиента. |
| timeout            | INT  | 30      | Время ожидания для установки подключения к серверу в секундах. |
| allow_redirects    | bool | False   | Включение перенаправлений. |
| allow_redirects      | INT  | 30      | Максимальное допустимое число перенаправлений. |
| proxies            | dict | {}      | URL-адрес прокси-сервера. См. дополнительные сведения в статье [Как конфигурировать прокси-серверы](azure-sdk-configure-proxy.md). |
| use_env_proxies    | bool | False   | Включение чтения параметров прокси-сервера из локальных переменных среды. |
| retries            | INT  | 10      | Общее число допустимых повторных попыток. |
| enable_http_logger | bool | False   | Включение журналов HTTP-протокола в режиме отладки. |

## <a name="inline-json-pattern-for-object-arguments"></a>Встроенная структура данных JSON для аргументов объектов

Библиотеки Azure позволяют выражать аргументы объектов в виде отдельных объектов или как встроенную структуру данных JSON.

Предположим, что существует объект [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient?view=azure-python), который создает группу ресурсов с помощью метода [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-). Второй аргумент для этого метода имеет тип [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup?view=azure-python).

Для вызова `create_or_update` можно создать отдельный экземпляр `ResourceGroup`, непосредственно указав его обязательные аргументы (в нашем случае `location`):

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    ResourceGroup(location="centralus")
)
```

Второй способ — это передать эти параметры в виде встроенной структуры данных JSON:

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    {
      "location": "centralus"
    }
)
```

При использовании JSON библиотеки Azure автоматически преобразуют встроенную структуру данных JSON в соответствующий тип объекта для нужного аргумента.

Объекты также могут иметь вложенные аргументы объектов. В этом случае можно также использовать встроенную структуру данных JSON.

Предположим, что у вас есть экземпляр объекта [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) и вы вызываете его метод [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-). В этом случае третий аргумент имеет тип [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), который содержит аргумент типа [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python). В свою очередь `VaultProperties` содержит аргументы объекта типа [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) и [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). `Sku` содержит объект [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python), а каждый `AccessPolicyEntry` содержит объект [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python).

Для вызова `create_or_update` с внедренными объектами используется код, подобный приведенному ниже (при условии, что `tenant_id` и `object_id` уже определены). Кроме того, необходимые объекты можно создать перед вызовом функции.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    VaultCreateOrUpdateParameters(
        location="centralus",
        properties=VaultProperties(
            tenant_id=tenant_id,
            sku=Sku(name="standard"),
            access_policies=[
                AccessPolicyEntry(
                    tenant_id=tenant_id,
                    object_id=object_id,
                    permissions=Permissions(keys=['all'], secrets=['all'])
                )
            ]
        )
    )
)
```

Этот же вызов с помощью встроенной структуры данных JSON выглядит следующим образом:

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    {
        'location': 'centralus',
        'properties': {
            'sku': {
                'name': 'standard'
            },
            'tenant_id': tenant_id,
            'access_policies': [{
                'object_id': object_id,
                'tenant_id': tenant_id,
                'permissions': {
                    'keys': ['all'],
                    'secrets': ['all']
                }
            }]
        }
    }
)
```

Так как оба способа дают одинаковый результат, вы можете выбрать любой или даже совместить их.

Если структура данных JSON задана неправильно, обычно возникает такая ошибка: "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'" (Ошибка десериализации: не удалось выполнить десериализацию в объект type. Ошибка атрибута: объект str не имеет атрибута get). Как правило, причина этой ошибки заключается в том, что вы предоставляете одну строку для свойства, тогда как библиотека ожидает вложенный объект JSON. Например, использование `"sku": "standard"` в предыдущем примере приводит к возникновению этой ошибки, так как параметр `sku` является объектом `Sku`, который ожидает встроенный объект JSON, в нашем случае `{ "name": "standard"}`, соответствующий соответствует ожидаемому типу `SkuName`.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знакомы с популярными схемами использования библиотек Azure для Python, ознакомьтесь со следующими автономными примерами, чтобы изучить конкретные сценарии для управления и клиентских библиотек.

- [Пример. Создание группы ресурсов](azure-sdk-example-resource-group.md)
- [Пример. Использование службы хранилища Azure](azure-sdk-example-storage.md)
- [Пример. Подготовка веб-приложения и развертывание кода](azure-sdk-example-web-app.md)
- [Пример. Подготовка базы данных к работе и выполнение запросов к ней](azure-sdk-example-database.md)
- [Пример. Подготовка виртуальной машины](azure-sdk-example-virtual-machines.md)

Эти примеры можно использовать в любом порядке, так как они не связаны между собой.
