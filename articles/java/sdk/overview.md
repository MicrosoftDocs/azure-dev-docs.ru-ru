---
title: Использование пакета Azure SDK для Java
description: Обзор функций и возможностей пакета Azure SDK для Java, которые позволяют повысить вашу продуктивность при подготовке, использовании и администрировании ресурсов Azure.
author: jonathangiles
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: jogiles
ms.openlocfilehash: 64a13c99f58b109a066acd322aa90f39a74ea0e9
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528629"
---
# <a name="use-the-azure-sdk-for-java"></a>Использование пакета Azure SDK для Java

Пакет Azure SDK для Java с открытым кодом упрощает подготовку, администрирование и использование ресурсов Azure из кода приложения Java.

## <a name="important-details"></a>Важные сведения

* Библиотеки Azure позволяют обмениваться данными со службами Azure из кода Java, который выполняется локально или в облаке.
* Библиотеки поддерживают Java 8 и более поздних версий. Также тестировалась их работа с Java 8 базового уровня и последним выпуском Java с долгосрочной поддержкой.
* Библиотеки включают полную поддержку модулей Java. Это означает, что они полностью соответствуют требованиям модуля Java и экспортируют все соответствующие пакеты для использования.
* в пакете Azure SDK содержится множество отдельных библиотек Java, которые предназначены для определенных служб Azure; В пакете SDK нет других средств.
* В пакете есть библиотеки двух типов: управления и клиентские (также известные как библиотеки "плоскости управления" и "плоскости данных"). Каждый тип имеет свое предназначение и используется с разными видами кода. Дополнительные сведения приведены в следующих разделах этой статьи:
  * [Подключение к ресурсам Azure и работа с ними с помощью клиентских библиотек.](#connect-to-and-use-azure-resources-with-client-libraries)
  * [Подготовка и администрирование ресурсов Azure с помощью библиотек управления.](#provision-and-manage-azure-resources-with-management-libraries)
* Документация по библиотекам доступна в [Справочнике по Azure для Java ](/java/api/overview/azure/), который упорядочен по службам Azure, или в [Обозревателе API Java](/java/api/), который упорядочен по именам пакетов.

## <a name="other-details"></a>Дополнительные сведения

* Библиотеки Azure SDK для Java построены поверх REST API Azure, что позволяет использовать эти API с помощью привычных парадигм Java. Но при необходимости вы всегда можете использовать REST API непосредственно из кода Java.
* Исходный код библиотек Azure доступен в [репозитории GitHub](https://github.com/Azure/azure-sdk-for-java). Так как это проект с открытым кодом, все желающие могут внести свой вклад.
* Сейчас мы обновляем библиотеки, входящие в пакет Azure SDK для Java, пополняя их облачными шаблонами для распространенных задач, таких как использование протоколов проверки подлинности, ведение журналов, трассировка, поддержка транспортных протоколов, буферизация ответов и выполнение повторных попыток.
  * Эта общая функциональная возможность содержится в библиотеке [azure-core](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core).
* Дополнительные сведения о рекомендациях по библиотеках см. в статье [Рекомендации по проектированию пакета Azure SDK для Java](https://azure.github.io/azure-sdk/java_introduction.html).

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Подключение к ресурсам Azure и работа с ними с помощью клиентских библиотек

С помощью клиентских библиотек (или библиотеки "плоскости данных") можно создавать код приложений Java для взаимодействия с уже подготовленными службами. Клиентские библиотеки доступны только для тех служб, которые поддерживают API клиента. Вы можете найти их по идентификатору группы Maven `com.azure`.

Все клиентские библиотеки Azure Java используют тот же конструктивный шаблон API, предоставляющий класс построителя Java, который отвечает за создание экземпляра клиента. Этот шаблон отделяет определение и экземпляр клиента от его операции. За счет экого клиент становится неизменяемым и более простым в использовании. Кроме того, все клиентские библиотеки следуют нескольким важным шаблонам:

* Клиентские библиотеки, поддерживающие как синхронные, так и асинхронные API, должны предоставлять эти API в отдельных классах. В этом случае для синхронного API будет предоставлен `KeyVaultClient`, а для асинхронного API — `KeyVaultAsyncClient`.

* Существует один класс построителя, отвечающий за создание синхронного и асинхронного API. Построитель имеет то же имя, что и класс клиента синхронизации с включенным `Builder`. Например, `KeyVaultClientBuilder`. Этот построитель содержит методы `buildClient()` и `buildAsyncClient()` для создания экземпляров клиента соответствующим образом.

Благодаря этим соглашениям все классы, заканчивающиеся на `Client`, являются неизменяемыми и предоставляют операции для взаимодействия со службой Azure. Все классы, заканчивающиеся на `ClientBuilder`, предоставляют операции настройки и создания экземпляра определенного типа клиента.

### <a name="client-libraries-example"></a>Пример клиентских библиотек

В следующем примере кода показано создание синхронного Key Vault `KeyClient`:

```java
KeyClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildClient();
```

В следующем примере кода показано создание асинхронного Key Vault `KeyAsyncClient`:

```java
KeyAsyncClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildAsyncClient();
```

Дополнительные сведения о работе с каждой клиентской библиотекой см. в файле *README.md*, расположенном в каталоге проекта библиотеки в [репозитории GitHub пакета SDK](https://github.com/Azure/azure-sdk-for-java). Дополнительные фрагменты кода также можно найти в [справочной документации](/java/api) и в разделе с [примерами для Azure](/samples/browse/?products=azure&languages=java).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Подготовка и администрирование ресурсов Azure с помощью библиотек управления

Библиотеки управления (или "плоскости управления") предназначены для создания, подготовки и администрирования ресурсов Azure из кода приложения Java. Вы можете найти эти библиотеки по идентификатору группы Maven `com.azure.resourcemanager`. У каждой службы Azure есть своя библиотека управления.

С помощью библиотек управления можно создавать скрипты конфигурации и развертывания для выполнения тех же задач, которые можно реализовать на [портале Azure](https://portal.azure.com/) или с помощью [Azure CLI](/cli/azure/install-azure-cli).

Все библиотеки управления Azure Java предоставляют класс `*Manager` в качестве API службы, например, `ComputeManager` для службы вычислений Azure и `AzureResourceManager` для агрегирования популярных служб.

### <a name="management-libraries-example"></a>Примеры библиотек управления

В следующем примере кода показано, как создать `ComputeManager`:

```java
ComputeManager computeManager = ComputeManager
    .authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE));
```

В следующем примере кода показано, как подготовить к работе новую виртуальную машину:

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .define(<your virtual machine>)
    .withRegion(Region.US_WEST)
    .withExistingResourceGroup(<your resource group>)
    .withNewPrimaryNetwork("10.0.0.0/28")
    .withPrimaryPrivateIPAddressDynamic()
    .withoutPrimaryPublicIPAddress()
    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_18_04_LTS)
    .withRootUsername(<virtual-machine username>)
    .withSsh(<virtual-machine SSH key>)
    .create();
```

В следующем примере кода показано, как получить существующую виртуальную машину:

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .getByResourceGroup(<your resource group>, <your virtual machine>);
```

В следующем примере кода показано, как обновить виртуальную машину и добавить новый диск данных:

```java
virtualMachine.update()
    .withNewDataDisk(10)
    .apply();
```

Дополнительные сведения о работе с каждой библиотекой управления см. в файле *README.md*, расположенном в каталоге проекта библиотеки в [репозитории GitHub пакета SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/resourcemanager#readme). Дополнительные фрагменты кода также можно найти в [справочной документации](/java/api) и в разделе с [примерами для Azure](/samples/browse/?products=azure&languages=java).

## <a name="get-help-and-connect-with-the-sdk-team"></a>Получение справки и связь с разработчиками SDK

* Ознакомьтесь с [документацией по пакету SDK Azure для Java](https://azure.github.io/azure-sdk-for-java/).
* Задавайте вопросы сообществу на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-for-java).
* Открытые проблемы с пакетом SDK [в репозитории GitHub](https://github.com/Azure/azure-sdk-for-java/issues).
* Упомянуть [@AzureSDK](https://twitter.com/AzureSdk/) в Twitter.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, что такое пакет Azure SDK для Java, вы можете подробно изучить многие из имеющихся сквозных концепций для повышения производительности при использовании библиотек. Приведенные ниже статьи являются хорошими отправными точками:

* [HTTP-клиенты и конвейеры](http-client-pipeline.md)
* [Асинхронное программирование](async-programming.md)
* [Разбиение на страницы и итерация](pagination.md)
* [Длительные операции](lro.md)
* [Настройка прокси-серверов](proxying.md)
* [Настройка трассировки](tracing.md)
