---
title: Руководство разработчика Java по библиотекам управления Azure
description: Шаблоны и способы использования библиотек управления Java для управления облачными ресурсами в Azure с помощью Java.
keywords: Azure, Java, SDK, API, Maven, Gradle, authentication, active directory, service principal
author: rloutlaw
ms.author: brendm
manager: douge
ms.date: 04/16/2017
ms.topic: article
ms.technology: azure
ms.devlang: java
ms.service: multiple
ms.assetid: f452468b-7aae-4944-abad-0b1aaf19170d
ms.openlocfilehash: c30ece40c8dd882a9e4c18fba953a7a8f16a7d11
ms.sourcegitcommit: 4cc7f5e1e4601065bfcb4c2eeb7d47ad0bec61f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68431050"
---
# <a name="patterns-and-best-practices-for-development-with-the-azure-libraries-for-java"></a>Шаблоны и рекомендации по разработке с помощью библиотек Azure для Java 

В этой статье описаны шаблоны и рекомендации по работе с библиотеками Azure для Java в ваших проектах. Разработка на основе этих шаблонов и рекомендаций позволит сократить объем поддерживающего кода и упростить добавление или настройку дополнительных ресурсов в будущих обновлениях библиотек управления.

## <a name="build-resources-through-a-fluent-interface"></a>Создание ресурсов с помощью текучего API

Текучий API — это шаблон, который создает объекты с использованием цепочки методов, правильно настраивающей атрибуты объекта. Например, создайте учетную запись хранения Azure:

```java
StorageAccount storage = azure.storageAccounts().define(storageAccountName)
    .withRegion(region)
    .withNewResourceGroup(resourceGroup)
    .create();
```

При выполнении цепочки методов ваша среда IDE предлагает для вызова следующий метод в процессе текучего обмена данными.   

![GIF-изображение: выполнение команды IntelliJ в текучей цепочке](media/intelliJFluent.gif)

Помещайте в цепочку методы, предлагаемые IDE, до тех пор, пока это требуется для определяемого ресурса Azure. Если вы пропустите обязательный метод в цепочке, IDE выделит его, отметив как ошибку.

## <a name="resource-collections"></a>Коллекции ресурсов

У библиотеки управления есть единая точка входа через объект `com.microsoft.azure.management.Azure` верхнего уровня для создания и обновления ресурсов. Выберите тип ресурсов для работы, используя методы коллекций ресурсов, определенные в объекте `Azure`. Например, база данных SQL:

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName)
    .withAdministratorLogin(administratorLogin)
    .withAdministratorPassword(administratorPassword)
    .create();
```

## <a name="lists-and-iterations"></a>Списки и итерации

Каждая коллекция ресурсов использует метод `list()`, который возвращает каждый экземпляр этого ресурса в текущей подписке. Например, `azure.sqlServers().list()` возвращает все базы данных SQL в подписке.

Используйте метод `listByResourceGroup(String groupname)`, чтобы ограничить возвращаемый список определенной [группой ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).  

Выполняйте поиск и итерацию для возвращаемой коллекции `PagedList<T>` так же, как для обычного списка `List<T>`:

```java
PagedList<VirtualMachine> vms = azure.virtualMachines().list();
for (VirtualMachine vm : vms) {
    System.out.println("Found virtual machine with ID " + vm.id());
}
```   

## <a name="collections-returned-from-queries"></a>Коллекции, возвращенные по запросам

Библиотеки управления возвращают определенные типы коллекций по запросам на основе структуры результатов.

- `List<T>`: неупорядоченные данные, позволяющие легко выполнять поиск и итерацию.
- `Map<T>`: сопоставлениями являются пары "ключ — значение" с уникальными ключами, но необязательно с уникальными значениями. Примером сопоставления будут параметры веб-приложения службы приложений.
- `Set<T>`: наборы имеют уникальные ключи и значения. Пример набора — сети, подключенные к виртуальной машине с уникальным идентификатором (ключом) и уникальной конфигурацией сети (значением).

## <a name="actionable-verbs"></a>Готовые к работе команды

Методы с командами в именах выполняются в Azure немедленно. Эти методы работают синхронно. Пока они не будут завершены, выполнение в текущем потоке блокируется. 

| Команда   |  Пример использования |
|--------|---------------|
| create | `azure.virtualMachines().create(listOfVMCreatables)` |
| apply  | `virtualMachineScaleSet.update().withCapacity(6).apply()` |
| удалить | `azure.disks().deleteById(id)` | 
| list   | `azure.sqlServers().list()` | 
| get    | `VirtualMachine vm  = azure.virtualMachines().getByResourceGroup(group, vmName)` |

>[!NOTE]
> `define()`и `update()` являются командами, но они не блокируют выполнение, если не сопровождаются `create()` или `apply()`.
 
Есть асинхронные версии некоторых из этих методов с суффиксом `Async`, которые используют [реактивные расширения](https://github.com/ReactiveX/RxJava). 

Для некоторых объектов применяются другие методы, с помощью которых изменяется состояние ресурса в Azure. Например, `restart()` или `VirtualMachine`.

```java
VirtualMachine vmToRestart = azure.getVirtualMachines().getById(id);
vmToRestart.restart();
```
Для этих методов не всегда есть асинхронные версии, поэтому выполнение в потоке блокируется до их завершения.

<a name="Creatables"></a>

## <a name="lazy-resource-creation"></a>Создание отложенных ресурсов

При создании ресурсов Azure возникает проблемная ситуация, когда новый ресурс зависит от другого ресурса, который еще не существует. Пример такого сценария — резервирование общедоступного IP-адреса и настройка диска при создании виртуальной машины. Предположим, вам не нужно проверять резервирование адреса или создание диска, вы просто хотите убедиться в том, что виртуальная машина располагает этими ресурсами при создании.

Объекты `Creatable<T>` позволяют определить ресурсы Azure для использования в коде до того, как они будут созданы в вашей подписке. Библиотеки управления откладывают создание объектов `Creatable<T>`, пока они не потребуются.

Создание объектов `Creatable<T>` для ресурсов Azure с помощью команды `define()`:

```java
Creatable<PublicIPAddress> publicIPAddressCreatable = azure.publicIPAddresses().define(publicIPAddressName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName);
```

Ресурс Azure, определенный `Creatable<PublicIPAddress>` в этом примере, еще не существует в вашей подписке при выполнении этого кода.  Используйте объект `publicIPAddressCreatable` для создания других ресурсов Azure с этим IP-адресом. 

```java
Creatable<VirtualMachine> vmCreatable = azure.virtualMachines().define("creatableVM")
    .withNewPrimaryPublicIPAddress(publicIPAddressCreatable)
```

Ресурсы `Creatable<T>` создаются в вашей подписке, когда любой ресурс, определенный с помощью объекта, собирается в Azure с использованием `create()`. В продолжение примера IP-адреса и виртуальной машины:

```java
CreatedResources<VirtualMachine> virtualMachine = azure.virtualMachines().create(vmCreatable);
```

Передача `Creatable<T>` в вызовы `create()` возвращает объект `CreatedResources` вместо объекта с одним ресурсом.  Объект `CreatedResources<T>` обеспечивает доступ ко всем ресурсам, созданным вызовом `create()`, а не только к типизированному ресурсу в вызове. Доступ к общедоступному IP-адресу, созданному в Azure для виртуальной машины, которая создается в примере выше:

```java
PublicIPAddress pip = (PublicIPAddress) virtualMachine.createdRelatedResource(publicIPAddressCreatable.key());
```    

## <a name="exception-handling"></a>Обработка исключений

Классы исключений для библиотек управления расширяют `com.microsoft.rest.RestException`. Перехват исключений, создаваемых библиотеками управления, выполняется с помощью блока `catch (RestException exception)` после соответствующей инструкции `try`.

## <a name="logs-and-trace"></a>Журналы и трассировка

Настройте объем данных, записываемых в журнал из библиотеки управления, когда создаете объект точки входа `Azure` с использованием `withLogLevel()`. Доступны следующие уровни трассировки:

| Уровень трассировки | Ведение журнала включено 
| ------------ | ---------------
| com.microsoft.rest.LogLevel.NONE | Нет выходных данных.
| com.microsoft.rest.LogLevel.BASIC | Регистрация URL-адресов для базовых вызовов REST, кодов и времени ответов.
| com.microsoft.rest.LogLevel.BODY | Регистрация тех же элементов, что и для уровня BASIC, а также текста запросов и ответов для вызовов REST.
| com.microsoft.rest.LogLevel.HEADERS | Регистрация тех же элементов, что и для уровня BASIC, а также заголовков запросов и ответов для вызовов REST.
| com.microsoft.rest.LogLevel.BODY_AND_HEADERS | Регистрация тех же элементов, что и для уровней журнала BODY и HEADERS в совокупности.

Для регистрации выходных данных привяжите [реализацию ведения журналов SLF4J](https://www.slf4j.org/manual.html) к платформе ведения журналов, например [Log4J 2](https://logging.apache.org/log4j/2.x/).