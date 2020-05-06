---
title: Параллельное создание виртуальных машин в разных регионах | Документация Майкрософт
description: Пример кода для параллельного создания виртуальных машин в разных регионах Azure с помощью пакета Azure SDK для Java
author: rloutlaw
ms.assetid: e5a36699-2d96-4571-84f9-a6af13f3c067
ms.topic: article
ms.date: 03/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 7e50a18346eb9bd2bee50bf2ea2d65cd94381292
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82104975"
---
# <a name="create-virtual-machines-across-multiple-regions-from-your-java-applications"></a>Создание виртуальных машин в нескольких регионах из приложений Java

[В этом примере](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel) виртуальные машины создаются параллельно в разных регионах Azure с помощью [библиотек управления Azure для Java](https://github.com/Azure/azure-sdk-for-java).

> [!IMPORTANT]
> В этом примере в четырех регионах создается 48 виртуальных машин [размером STANDARD_DS3_V2](/azure/virtual-machines/virtual-machines-windows-sizes) под управлением LTS Ubuntu 16.04. Этот пример кода удаляет виртуальные машины перед завершением работы. Обязательно [проверьте квоты и ограничения службы](/azure/azure-subscription-service-limits), прежде чем выполнять этот пример с определенным по умолчанию числом виртуальных машин.

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel.git
cd compute-java-create-virtual-machines-across-regions-in-parallel
mvn clean compile exec:java
```

Просмотрите [полный пример кода на GitHub](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/CreateVirtualMachinesInParallel.java).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="set-locations-and-counts-for-the-virtual-machines"></a>Определение расположения и счетчиков для виртуальных машин

```java
// use a Map to define how where and how many VMs to create 
Map<Region, Integer> virtualMachinesByLocation = new HashMap<Region, Integer>();

// create 12 virtual machines in four regions
virtualMachinesByLocation.put(Region.US_EAST, 12);
virtualMachinesByLocation.put(Region.US_SOUTH_CENTRAL, 12);
virtualMachinesByLocation.put(Region.US_WEST, 12);
virtualMachinesByLocation.put(Region.US_NORTH_CENTRAL, 12);
```

`Map` используется позже в этом примере для распределения виртуальных машин по всему миру.

## <a name="create-a-resource-group"></a>Создание группы ресурсов 

```java
// logically associate the resources in the sample into a randomly named resource group
final String rgName = SdkContext.randomResourceName("rgCOPD", 24);
ResourceGroup resourceGroup = azure.resourceGroups().define(rgName)
                .withRegion(Region.US_EAST)
                .create();
```

Эта группа ресурсов управляет каждым ресурсом в примере, что упрощает последующую очистку ресурсов. Для этого достаточно удалить группу ресурсов.

## <a name="define-the-virtual-machines"></a>Определение виртуальных машин
```java
// list to store the VirtualMachine definitions
List<Creatable<VirtualMachine>> creatableVirtualMachines = new ArrayList<>();
    
// outer loop: iterate through each region included in the map
for (Map.Entry<Region, Integer> entry : virtualMachinesByLocation.entrySet()) {
    Region region = entry.getKey();
    Integer vmCount = entry.getValue();

    // Define one virtual network Creatable per region for the VMs to share
    String networkName = SdkContext.randomResourceName("vnetCOPD-", 20);
    Creatable<Network> networkCreatable = azure.networks().define(networkName)
           .withRegion(region)
           .withExistingResourceGroup(resourceGroup)
           .withAddressSpace("172.16.0.0/16");

    // Define one storage account Creatable per region for storing VM disks
    String storageAccountName = SdkContext.randomResourceName("stgcopd", 20);
    Creatable<StorageAccount> storageAccountCreatable = azure.storageAccounts()
        .define(storageAccountName)
              .withRegion(region)
              .withExistingResourceGroup(resourceGroup);

    // generate a common prefix for every VM name
    String linuxVMNamePrefix = SdkContext.randomResourceName("vm-", 15);

    // inner loop: iterate once for every VM instance in the region
    for (int i = 1; i <= vmCount; i++) {

        // Create one public IP address Creatable for each VM
        Creatable<PublicIpAddress> publicIpAddressCreatable = azure.publicIpAddresses()
             .define(String.format("%s-%d", linuxVMNamePrefix, i))
             .withRegion(region)
             .withExistingResourceGroup(resourceGroup)
             .withLeafDomainLabel(SdkContext.randomResourceName("pip", 10));

        publicIpCreatableKeys.add(publicIpAddressCreatable.key());

        // Create one virtual machine Creatable 
        Creatable<VirtualMachine> virtualMachineCreatable = azure.virtualMachines()
             .define(String.format("%s-%d", linuxVMNamePrefix, i))
             .withRegion(region)
             .withExistingResourceGroup(resourceGroup)
             .withNewPrimaryNetwork(networkCreatable)
             .withPrimaryPrivateIpAddressDynamic()
             .withNewPrimaryPublicIpAddress(publicIpAddressCreatable)
             .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
             .withRootUsername(userName)
             .withSsh(sshKey)
             .withSize(VirtualMachineSizeTypes.STANDARD_DS3_V2)
             .withNewStorageAccount(storageAccountCreatable);
        // add the virtual machine Creatable to the list     
        creatableVirtualMachines.add(virtualMachineCreatable); 
     }
}
```

Для внешнего цикла `for` выше выполняется итерация по каждому региону. При этом определяются объекты Creatable для виртуальной сети и учетной записи хранилища, которые будут использоваться всеми виртуальными машинами в этом регионе. Узнайте больше о применении [объектов Creatable](java-sdk-azure-concepts.md#Creatables), которые позволяют создавать ресурсы при использовании библиотек управления только в случае необходимости.

Внутренний цикл `for` получает объект Creatable для общедоступного IP-адреса виртуальной машины и определяет объект Creatable для виртуальной машины. При этом используются объекты Creatable для виртуальной сети, учетной записи хранения и общедоступного IP-адреса, определенного ранее.  Затем объект Creatable VirtualMachine добавляется в список `creatableVirtualMachines`.

## <a name="create-the-virtual-machines"></a>Создание виртуальных машин

```java
// create all virtual machines defined in the list, return all Creatable objects used
// including networks, public IP addresses, and storage accounts
CreatedResources<VirtualMachine> virtualMachines = azure.virtualMachines().create(creatableVirtualMachines);

// list the IDs of each virtual machine created 
for (VirtualMachine virtualMachine : virtualMachines.values()) {
    System.out.println(virtualMachine.id());
}

// call createdRelatedResource(key) to get the resources used to define the virtual machines. 
// Save the key at the time you define the Creatable to use CreatedResources like this
for (String publicIpCreatableKey : publicIpCreatableKeys) {
    PublicIPAddress pip = 
         (PublicIPAddress) virtualMachines.createdRelatedResource(publicIpCreatableKey);
}
```

Вызов `azure.virtualMachines().create(creatableVirtualMachines)` параллельно создает все виртуальные машины, определенные в списке `creatableVirtualMachines` во всех регионах.

Используйте возвращаемый объект `CreatedResources<VirtualMachine>` для доступа к любым ресурсам, созданным в подписке Azure при выполнении метода `create()`, а не только к ресурсу с возвращаемым значением типа `VirtualMachine`. Измените `createdRelatedResources()` на значение надлежащего типа. 

Дополнительные сведения о работе с `Creatable<T>` и `CreatedResources` см. в нашей [статье о принципах работы библиотек](java-sdk-azure-concepts.md).

## <a name="delete-the-resource-group"></a>удаление группы ресурсов. 

```java
// finally block deletes the resource group before the code exits
// deleting a resource group deletes all resources created in it
finally {
    try {
        System.out.println("Deleting Resource Group: " + rgName);
        azure.resourceGroups().deleteByName(rgName);
        System.out.println("Deleted Resource Group: " + rgName);
    } catch (NullPointerException npe) {
        System.out.println("Did not create any resources in Azure. No clean up is necessary");
    } catch (Exception g) {
        g.printStackTrace();
    }
}
```

Этот блок удаляет ресурсы, созданные в примере, перед выходом из него.

## <a name="sample-explanation"></a>Описание примера

См. полный пример кода на [GitHub](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel).

В примере используются объекты `Creatable`, чтобы определить виртуальную сеть и учетную запись хранения для каждого региона, в котором размещены виртуальные машины. Затем определяется объект `Creatable` для общедоступного IP-адреса каждой виртуальной машины. В примере с использованием этих объектов `Creatable` определяются виртуальные машины, а их определения добавляются в список `virtualMachineCreatable`.

Когда в список будут добавлены все определения, `azure.virtualMachines().create(creatableVirtualMachines)` параллельно создаст все виртуальные машины в Azure.

Затем пример кода получает IP-адреса от возвращаемого объекта CreatedResources для всех созданных виртуальных машин. Это позволяет создать [диспетчер трафика](/azure/traffic-manager/traffic-manager-overview) для распределения нагрузки между виртуальными машинами. 

Блок `finally` удаляет ресурсы из подписки Azure даже в случае ошибки.

| Класс, используемый в примере | Примечания
|-------|-------|
| [VirtualMachine](/java/api/com.microsoft.azure.management.compute.virtualmachine) | Запрос свойств и управление состоянием виртуальных машин. Извлекается в виде списка из `azure.virtualMachines().list()` или по имени либо идентификатору `azure.virtualMachines().getByResourceGroup()`
| [VirtualMachineSizeTypes](/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | Статические значения, которые сопоставляются с [параметрами размера виртуальной машины](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) и используются как параметры для `withSize()` при определении виртуальной машины.
| [PublicIpAddress](/java/api/com.microsoft.azure.management.network.publicipaddress) | Определяется (но не создается немедленно) для каждой виртуальной машины с помощью `azure.publicIpAddresses().define()`. Сохраните ключ для каждого `Creatable` и выполните извлечение позднее с помощью `createdRelatedResource()`
| [KnownLinuxVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | Набор параметров виртуальной машины Linux, которые используются в качестве параметров для метода `withPopularLinuxImage()` при определении виртуальной машины.
| [Network](/java/api/com.microsoft.azure.management.network.network) | В примере определяется одна виртуальная сеть для каждого региона с помощью `azure.networks().define()`. 

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [next-steps](includes/java-next-steps.md)]