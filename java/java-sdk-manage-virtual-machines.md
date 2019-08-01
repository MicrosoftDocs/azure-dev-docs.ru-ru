---
title: Управление виртуальными машинами Azure с помощью Java | Документация Майкрософт
description: Пример кода для управления виртуальными машинами Azure с помощью пакета Azure SDK для Java
author: rloutlaw
manager: douge
ms.assetid: 88629aee-6279-433e-a08b-4f8e290446d0
ms.devlang: java
ms.topic: article
ms.service: azure
ms.date: 3/30/2017
ms.author: brendm
ms.reviewer: asirveda
ms.openlocfilehash: 4cba24f92d5caaf244127536d231a5f942cb3913
ms.sourcegitcommit: f799dd4590dc5a5e646d7d50c9604a9975dadeb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68691828"
---
# <a name="manage-azure-virtual-machines-from-your-java-applications"></a>Управление виртуальными машинами Azure из приложений Java

[Этот пример](https://github.com/Azure-Samples/compute-java-manage-vm/) использует [библиотеки управления Azure для Java](https://github.com/Azure/azure-sdk-for-java) для создания виртуальных машин Azure и управления ими.

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/compute-java-manage-vm.git
cd compute-java-manage-vm
mvn clean compile exec:java
```

Просмотрите [полный пример кода на GitHub](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-windows-virtual-machine"></a>Создание виртуальной машины Windows

```java
// Prepare a data disk for VM
Disk dataDisk = azure.disks().define(SdkContext.randomResourceName("dsk", 30))
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withData()
            .withSizeInGB(50)
            .create();

// create the windows virtual machine with the data disk            
VirtualMachine windowsVM = azure.virtualMachines().define(windowsVmName)
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withNewPrimaryNetwork("10.0.0.0/28")
            .withPrimaryPrivateIpAddressDynamic()
            .withoutPrimaryPublicIpAddress()
            .withPopularWindowsImage(KnownWindowsVirtualMachineImage.WINDOWS_SERVER_2012_R2_DATACENTER)
            .withAdminUsername(userName)
            .withAdminPassword(password)
            .withNewDataDisk(10)
            .withNewDataDisk(dataDiskCreatable)
            .withExistingDataDisk(dataDisk)
            .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
            .create();
```

Этот код выполняет следующие действия:   

0. Определяет создаваемый объект `Disk` с произвольным именем и размером 50 ГБ, который будет использоваться с виртуальной машиной.
0. Использует цепочку `azure.virtualMachines().define()..create()` для создания виртуальной машины Windows Server 2012. API создает `Disk`, определенный на предыдущем шаге одновременно с виртуальной машиной. С помощью `withNewDataDisk(10)` к виртуальной машине также подключается диск данных размером 10 ГБ.

См. дополнительные сведения об использовании [создаваемых<T> объектов](java-sdk-azure-concepts.md#Creatables), чтобы определить локальные представления ресурсов и создавать их, когда он будут нужны другим ресурсам Azure.

## <a name="stop-start-and-restart-a-virtual-machine"></a>Остановка, запуск и перезапуск виртуальной машины

```java
// look up a virtual machine by its ID and then restart, stop, and start it
azureVM = azure.getVirtualMachine.getById(windowsVM.id());

azureVM.restart();
azureVM.powerOff();
azureVM.start();
```

`powerOff()` останавливает операционную систему виртуальной машины, но не освобождает ее ресурсы.

## <a name="add-a-virtual-machine-to-an-existing-network"></a>Добавление виртуальной машины в существующую сеть

```java
// Get the virtual network the current virtual machine is using
Network network = windowsVM.getPrimaryNetworkInterface().primaryIPConfiguration().getNetwork();

// Create a Linux VM in the same subnet
VirtualMachine linuxVM = azure.virtualMachines().define(linuxVmName)
           .withRegion(region)
           .withExistingResourceGroup(rgName)
           .withExistingPrimaryNetwork(network)
           .withSubnet("subnet1") // default subnet name when no name specified at creation
           .withPrimaryPrivateIPAddressDynamic()
           .withoutPrimaryPublicIPAddress()
           .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
           .withRootUsername(userName)
           .withRootPassword(password)
           .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
           .create();
```

Используйте `withPopularLinuxImage`, чтобы определить виртуальную машину Linux вместо Windows.


## <a name="list-virtual-machines"></a>Отображение списка виртуальных машин

```java
// get a list of VMs in the same resource group as an existing VM
String resourceGroupName = windowsVM.resourceGroupName();
PagedList<VirtualMachine> resourceGroupVMs = azure.virtualMachines()
        .listByResourceGroup(resourceGroupName); 

// for each vitual machine in the resource group, log their name and plan
for (VirtualMachine virtualMachine : azure.virtualMachines().listByResourceGroup(resourceGroupName)) {
    System.out.println("VM " + virtualMachine.computerName() + 
        " has plan " + virtualMachine.plan());
}
```

Чтобы отобразить список всех виртуальных машин в подписке, используйте `azure.virtualMachines().list()`. Выполните итерацию по схеме, возвращенной с помощью `tags()`, чтобы управлять помеченными тегами коллекциями виртуальных машин в группах ресурсов.

## <a name="update-a-virtual-machine"></a>Обновление виртуальной машины

```java
// add a 10GB data disk to the virtual machine
windowsVM.update()
     .withNewDataDisk(10)
     .apply();
```

Обновите конфигурацию виртуальной машины с помощью `update()...apply()` и методов, которые использовались для настройки виртуальной машины при создании с помощью `define()...create()`.

## <a name="delete-a-virtual-machine"></a>удаление виртуальной машины

```java
// delete by ID if you already are working with the VM object
azure.virtualMachines().deleteById(windowsVM.id());

// delete by resource group and name
azure.virtualMachines().deleteByResourceGroup(rgName,windowsVmName);
```

## <a name="sample-explanation"></a>Описание примера

[Пример кода](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java) создает виртуальную машину Windows с диском данных размером 50 ГБ. Затем пример создает второй диск данных размером 10 ГБ и подключает его к этой виртуальной машине Windows.
Затем пример создает виртуальную машину Linux в той же виртуальной сети, в которой создана виртуальная машина Windows.

Пример записывает сведения о виртуальных машинах и в завершение удаляет обе машины.

| Класс, используемый в примере | Примечания
|-------|-------|
| [VirtualMachine](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._virtual_machine) | Запрос свойств и управление состоянием виртуальных машин. Извлекается в виде списка с помощью `azure.virtualMachines().list()` или по имени либо идентификатору `azure.virtualMachines().getByResourceGroup()`
| [VirtualMachineSizeTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._virtual_machine_size_types) | Класс со статическими значениями, сопоставленными с [параметрами размера виртуальной машины](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), используемыми в методе `withSize()` для определения ресурсов, выделенных для виртуальной машины.
| [Диск](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._disk) | Создание диска для хранения данных с помощью `withData()` или образа операционной системы с помощью соответствующего метода `withLinux` либо `withWindows` при определении диска. Подключение дисков к виртуальным машинам во время создания (`using withNewDataDisk` или `withExistingDataDisk`) или после создания с помощью `update()..apply()` в объекте VirtualMachine.
| [DiskSkuTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._disk_sku_types) | Класс статических значений для определения диска с помощью тарифного плана хранилища уровня "Стандартный" или ["Премиум"](https://docs.microsoft.com/azure/storage/storage-premium-storage).
| [KnownLinuxVirtualMachineImage](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._known_linux_virtual_machine_image) | Класс с набором параметров виртуальной машины Linux, используемых с методом `withPopularLinuxImage()` при определении виртуальной машины.
| [KnownWindowsVirtualMachineImage](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._known_windows_virtual_machine_image) | Класс с набором параметров виртуальной машины Windows, используемых с методом `withPopularWindowsImage()` при определении виртуальной машины.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [next-steps](includes/java-next-steps.md)]