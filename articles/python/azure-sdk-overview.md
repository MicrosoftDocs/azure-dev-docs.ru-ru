---
title: Использование пакета Azure SDK для Python
description: Обзор функций и возможностей пакета Azure SDK для Python, которые позволяют повысить продуктивность разработчиков при подготовке, использовании и администрировании ресурсов Azure.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 856487b06e7a84b0659126d8959ce45b5309a103
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631547"
---
# <a name="use-the-azure-sdk-for-python"></a>Использование пакета Azure SDK для Python

Пакет Azure SDK для Python с открытым кодом упрощает подготовку, администрирование и использование ресурсов Azure из кода приложения Python.

## <a name="the-details-you-really-want-to-know"></a>Основные сведения

- Пакет SDK поддерживает Python 2.7 и Python 3.5.3 или более поздней версии. Также тестировалась его работа с PyPy 5.4 и последующих версий.

- В пакете содержится более 180 библиотек Python, которые предназначены для определенных служб Azure.

- Необходимые библиотеки устанавливаются с помощью команды `pip install <library_name>`, в которой указывается имя библиотеки из [списка выпусков](https://azure.github.io/azure-sdk/releases/latest/all/python.html). Дополнительные сведения см. в статье [Установка пакета Azure SDK для библиотек Python](azure-sdk-install.md).

- В пакете есть библиотеки двух типов: управления и клиентские (также известные как библиотеки "плоскости управления" и "плоскости данных"). Каждый тип имеет свое предназначение и используется с разными видами кода. Дополнительные сведения приведены в следующих разделах этой статьи:
  - [Подготовка и администрирование ресурсов Azure с помощью библиотек управления](#provision-and-manage-azure-resources-with-management-libraries)
  - [Подключение к ресурсам Azure и работа с ними с помощью клиентских библиотек](#connect-to-and-use-azure-resources-with-client-libraries)

- Документация по пакету SDK доступна в [Справочнике по Azure SDK для Python](/python/api/overview/azure/?view=azure-python), который упорядочен по службам Azure, или в [Обозревателе API Python](/python/api/?view=azure-python), который упорядочен по именам пакетов. Чтобы найти сведения о нужных классах и методах, сейчас потребуется выполнить не один переход со страницы на страницу. Заранее приносим извинения за эти неудобства. Сейчас мы стараемся оптимизировать структуру документации.

- Чтобы проверить работу библиотек, выполните инструкции из статей [Пример создания группы ресурсов](azure-sdk-example-resource-group.md) и [Пример использования службы хранилища Azure](azure-sdk-example-storage.md).

### <a name="non-essential-but-still-interesting-details"></a>Дополнительные сведения

- Поскольку интерфейс командной строки (CLI) Azure написан на языке Python с использованием библиотек управления из пакета SDK, любые действия с командами Azure CLI можно также выполнить с помощью скрипта Python. С другой стороны, команды CLI обеспечивают ряд полезных возможностей, таких как одновременное выполнение нескольких задач, автоматическая обработка асинхронных операций, форматирование выходных данных, например строк подключения, и т. д. Следовательно, использование CLI (или его аналога — Azure PowerShell) для автоматизированной подготовки и администрирования скриптов может быть значительно удобнее, чем написание кода Python для выполнения этих функций, если только вам не требуется более строгий контроль над процессом.

- В пакете Azure SDK для Python уровень языка Python расположен поверх REST API Azure, что позволяет использовать эти API с помощью привычных парадигм Python. Но при необходимости вы всегда можете использовать REST API непосредственно из кода Python.

- Исходный код пакета SDK доступен по адресу [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Так как это проект с открытым кодом, все желающие могут внести свой вклад.

- Пакет SDK можно использовать с интерпретаторами, такими как IronPython и Jython, с которыми он не тестировался, но при этом вы можете столкнуться с отдельными проблемами и несовместимостью.

- Исходный репозиторий с документацией по пакету SDK доступен по адресу [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/).

- Сейчас мы обновляем библиотеки, входящие в пакет Azure SDK для Python, пополняя их облачными шаблонами для распространенных задач, таких как использование протоколов проверки подлинности, ведение журналов, трассировка, поддержка транспортных протоколов, буферизация ответов и выполнение повторных попыток.

  - Эта общая функциональная возможность содержится в библиотеке [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core).

  - Библиотеки, которые сейчас используются с основной библиотекой, доступны на странице [последних выпусков пакета Azure SDK для Python](https://azure.github.io/azure-sdk/releases/latest/#python). Это в основном клиентские библиотеки, также известные как track 2.

  - Библиотеки управления, которые известны как track 1, пока не обновлялись.

- Дополнительные сведения о рекомендациях по пакету SDK см. в [Руководстве по Python: введение](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Подготовка и администрирование ресурсов Azure с помощью библиотек управления

Библиотеки *управления* (или "плоскости управления") из пакета SDK, имена которых начинаются с `azure-mgmt-`, предназначены для создания, подготовки и администрирования ресурсов Azure из скриптов Python. У каждой службы Azure есть своя библиотека управления.

С помощью библиотек управления можно создавать скрипты конфигурации и развертывания для выполнения тех же задач, которые можно выполнять на [портале Azure](https://portal.azure.com) или с помощью [Azure CLI](/cli/azure/install-azure-cli). (Как отмечалось ранее, интерфейс командной строки Azure написан на Python и использует библиотеки управления для реализации различных команд.)

Дополнительные сведения о работе с каждой библиотекой управления см. в файле *README.md* или *README.rst*, расположенном в папке проекта библиотеки в [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) пакета SDK. Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python) и в разделе с [примерами для Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Подключение к ресурсам Azure и работа с ними с помощью клиентских библиотек

С помощью *клиентских* библиотек (или библиотеки "плоскости данных") из пакета SDK можно создавать код приложений Python для взаимодействия с уже подготовленными службами. В пакете SDK доступны клиентские библиотеки только для тех служб, которые поддерживают API клиента.

Дополнительные сведения о работе с каждой клиентской библиотекой см. в файле *README.md* или *README.rst*, расположенном в папке проекта библиотеки в [репозитории GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) пакета SDK. Дополнительные фрагменты кода также можно найти в [справочной документации](/python/api?view=azure-python) и в разделе с [примерами для Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="inline-json-pattern-for-object-arguments"></a>Встроенная структура данных JSON для аргументов объектов

В пакете Azure SDK многие операции поддерживают такую распространенную схему, которая позволяет выражать аргументы объектов в виде отдельных объектов или как встроенную структуру данных JSON.

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

При использовании JSON пакет SDK автоматически преобразует встроенную структуру данных JSON в соответствующий тип объекта для нужного аргумента.

Объекты также могут иметь вложенные аргументы объектов. В этом случае можно также использовать встроенную структуру данных JSON.

Предположим, что у вас есть экземпляр объекта [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) и вы вызываете его метод [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-). В этом случае третий аргумент имеет тип [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), который содержит аргумент типа [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python). В свою очередь `VaultProperties` содержит аргументы объекта типа [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) и [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). `Sku` содержит объект [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python), а каждый `AccessPolicyEntry` содержит объект [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python).

Для вызова `create_or_update` с внедренными объектами используется код, подобный приведенному ниже (при условии, что `tenant_id` и `object_id` уже определены).

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

Поскольку оба способа дают одинаковый результат, можно выбрать любой из них и даже совмещать их.

Если структура данных JSON задана неправильно, обычно возникает такая ошибка: "DeserializationError: Unable to deserialize to object: type, AttributeError: 'str' object has no attribute 'get'" (Ошибка десериализации: не удалось выполнить десериализацию в объект type. Ошибка атрибута: объект str не имеет атрибута get). Как правило, причина этой ошибки заключается в том, что вы предоставляете одну строку для свойства, тогда как пакет SDK ожидает вложенный объект JSON. Например, использование `"sku": "standard"` в предыдущем примере приводит к возникновению этой ошибки, так как параметр `sku` является объектом `Sku`, который ожидает встроенный объект JSON, в нашем случае `{ "name": "standard"}`, соответствующий соответствует ожидаемому типу `SkuName`.

## <a name="next-step"></a>Следующий шаг

> [!div class="nextstepaction"]
> [Установка библиотек SDK >>>](azure-sdk-install.md)

## <a name="get-help-and-connect-with-the-sdk-team"></a>Получение справки и связь с разработчиками SDK

- См. [документацию по пакету SDK Azure для Python](https://aka.ms/python-docs)
- Задавайте вопросы сообществу на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Открытые проблемы с пакетом SDK на [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Упомянуть [@AzureSDK](https://twitter.com/AzureSdk/) в Twitter
