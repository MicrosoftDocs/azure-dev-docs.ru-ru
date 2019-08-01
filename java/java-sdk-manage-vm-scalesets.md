---
title: Управление масштабируемыми наборами виртуальных машин с помощью Java | Документация Майкрософт
description: Пример кода для управления масштабируемыми наборами виртуальных машин Azure с помощью пакета Azure SDK для Java
author: rloutlaw
manager: douge
ms.assetid: b55923b7-d60a-460d-b77c-af5fac67f1cc
ms.devlang: java
ms.topic: article
ms.service: azure
ms.date: 3/30/2017
ms.author: brendm
ms.reviewer: asirveda
ms.openlocfilehash: af56ef8975d0ec74ef32cf6deadea0e4c562e0c4
ms.sourcegitcommit: f799dd4590dc5a5e646d7d50c9604a9975dadeb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68691804"
---
# <a name="manage-azure-virtual-machine-scale-sets-from-your-java-applications"></a>Управление масштабируемыми наборами виртуальных машин Azure из приложений Java

[Этот пример](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets) создает [масштабируемый набор виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) с помощью [библиотек управления Java](https://github.com/Azure/azure-sdk-for-java). 

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets.git
cd compute-java-manage-virtual-machine-scale-sets
mvn clean compile exec:java
```

Просмотрите [полный пример кода на GitHub](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-virtual-network-for-the-scale-set"></a>Создание виртуальной сети для масштабируемого набора

```java
Network network = azure.networks().define(vnetName)
                    .withRegion(region)
                    .withNewResourceGroup(rgName)
                    .withAddressSpace("172.16.0.0/16")
                    .defineSubnet("Front-end")
                    .withAddressPrefix("172.16.1.0/24")
                    .attach()
                    .create();
```

Настройте виртуальную сеть и подсистему балансировки нагрузки, прежде чем создавать определение масштабируемого набора. Масштабируемый набор использует эти ресурсы для начальной конфигурации.

## <a name="create-a-load-balancer-to-distribute-load-across-the-scale-set"></a>Создание подсистемы балансировки нагрузки для распределения нагрузки по всему масштабируемому набору

```java
LoadBalancer loadBalancer1 = azure.loadBalancers().define(loadBalancerName1)
                    .withRegion(region)
                    .withExistingResourceGroup(rgName)
                    // assign a public IP address to the load balancer
                    .definePublicFrontend(frontendName)
                        .withExistingPublicIPAddress(publicIPAddress)
                        .attach()
                    // Add two backend address pools
                    .defineBackend(backendPoolName1)
                        .attach()
                    .defineBackend(backendPoolName2)
                        .attach()
                    // Add two health probes on 80 and 443
                    .defineHttpProbe(httpProbe)
                        .withRequestPath("/")
                        .withPort(80)
                        .attach()
                    .defineHttpProbe(httpsProbe)
                        .withRequestPath("/")
                        .withPort(443)
                        .attach()

                    // balance HTTP and HTTPs traffic
                    .defineLoadBalancingRule(httpLoadBalancingRule)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPort(80)
                        .withProbe(httpProbe)
                        .withBackend(backendPoolName1)
                        .attach()
                    .defineLoadBalancingRule(httpsLoadBalancingRule)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPort(443)
                        .withProbe(httpsProbe)
                        .withBackend(backendPoolName2)
                        .attach()

                    // Add NAT definitions to enable SSH and telnet to the VMs 
                    .defineInboundNatPool(natPool50XXto22)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPortRange(5000, 5099)
                        .withBackendPort(22)
                        .attach()
                    .defineInboundNatPool(natPool60XXto23)
                        .withProtocol(TransportProtocol.TCP)
                        .withFrontend(frontendName)
                        .withFrontendPortRange(6000, 6099)
                        .withBackendPort(23)
                        .attach()
                    .create();
```

 Подсистема балансировки нагрузки определяет два пула внутренних сетевых адресов: один для балансировки нагрузки по протоколу HTTP (`backendPoolName1`), другой — для балансировки нагрузки по протоколу HTTPS (`backendPoolName2`).  Методы `defineHttpProbe()` настраивают [конечные точки проверки работоспособности](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) в подсистемах балансировки нагрузки. Правила преобразования сетевых адресов предоставляют порты 22 и 23 на виртуальных машинах масштабируемых наборов для доступа с помощью telnet и SSH.

## <a name="create-a-scale-set"></a>Создание масштабируемого набора
 
```java
 // Create a virtual machine scale set with three virtual machines
 // And, install Apache Web servers on them
VirtualMachineScaleSet virtualMachineScaleSet = azure.virtualMachineScaleSets()
       .define(vmssName)
                .withRegion(region)
                .withExistingResourceGroup(rgName)
                .withSku(VirtualMachineScaleSetSkuTypes.STANDARD_D3_V2)
                .withExistingPrimaryNetworkSubnet(network, "Front-end")
                .withExistingPrimaryInternetFacingLoadBalancer(loadBalancer1)
                .withPrimaryInternetFacingLoadBalancerBackends(backendPoolName1, backendPoolName2)
                .withPrimaryInternetFacingLoadBalancerInboundNatPools(natPool50XXto22, natPool60XXto23)
                .withoutPrimaryInternalLoadBalancer()
                .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                .withRootUsername(userName)
                .withSsh(sshKey)
                .withNewDataDisk(100)
                .withNewDataDisk(100, 1, CachingTypes.READ_WRITE)
                .withNewDataDisk(100, 2, 
                     CachingTypes.READ_WRITE, StorageAccountTypes.STANDARD_LRS)
                .withCapacity(3)
                // Use a VM extension to install Apache Web servers
                .defineNewExtension("CustomScriptForLinux")
                    .withPublisher("Microsoft.OSTCExtensions")
                    .withType("CustomScriptForLinux")
                    .withVersion("1.4")
                    .withMinorVersionAutoUpgrade()
                    .withPublicSetting("fileUris", fileUris)
                    .withPublicSetting("commandToExecute", installCommand)
                    .attach()
                .create();
```

Используйте определение виртуальной сети и определения подсистемы балансировки нагрузки, созданные на предыдущем шаге, чтобы создать масштабируемый набор с тремя экземплярами Linux (`withCapacity(3)`) и тремя дисками данных размером по 100 ГБ. Цепочка методов `defineNewExtension()` устанавливает веб-сервер Apache на каждой виртуальной машине.

## <a name="list-virtual-machine-scale-set-network-interfaces"></a>Получение списка сетевых интерфейсов масштабируемого набора виртуальных машин

```java
// List network interfaces on the scale set and iterate through them
PagedList<VirtualMachineScaleSetNetworkInterface> vmssNics = 
     virtualMachineScaleSet.listNetworkInterfaces();
for (VirtualMachineScaleSetNetworkInterface vmssNic : vmssNics) {
    System.out.println(vmssNic.id());
}
```

## <a name="get-ssh-connection-strings-for-each-scale-set-virtual-machine"></a>Получение строк подключения SSH для каждой виртуальной машины в масштабируемом наборе

```java
for (VirtualMachineScaleSetVM instance : virtualMachineScaleSet.virtualMachines().list()) {
    System.out.println("Scale set virtual machine instance #" + instance.instanceId());
    System.out.println(instance.id());
    PagedList<VirtualMachineScaleSetNetworkInterface> networkInterfaces = 
         instance.listNetworkInterfaces();
    // Pick the first NIC on the instance and use its primary IP address
    VirtualMachineScaleSetNetworkInterface networkInterface = networkInterfaces.get(0);
    for (VirtualMachineScaleSetNicIPConfiguration ipConfig : networkInterface.ipConfigurations().values()) {
        if (ipConfig.isPrimary()) {
            List<LoadBalancerInboundNatRule> natRules = ipConfig.listAssociatedLoadBalancerInboundNatRules();
            for (LoadBalancerInboundNatRule natRule : natRules) {
                // find rule matching the inbound SSH port on the backend for the IP address
                // and return the SSH connection string to that port on the load balancer
                if (natRule.backendPort() == 22) {
                    System.out.println("SSH connection string: " + userName + 
                        "@" + publicIPAddress.fqdn() + ":" + natRule.frontendPort());
                break;
                }
            }
            break;
        }
    }
}
```

Пул преобразования сетевых адресов, созданный ранее, сопоставляет порты SSH и telnet (22 и 23 соответственно) на виртуальных машинах с портами в подсистеме балансировки нагрузки. Этот код создает строку подключения SSH для каждой виртуальной машины.

## <a name="stop-the-virtual-machine-scale-set"></a>Остановка масштабируемого набора виртуальных машин

```java
// stop (not deallocate) all scale set instances
virtualMachineScaleSet.powerOff();
```

Остановленные виртуальные машины по-прежнему потребляют зарезервированные ресурсы. Используйте команду `deallocate()`, чтобы остановить операционную систему на виртуальных машинах и освободить соответствующие вычислительные ресурсы.

## <a name="deallocate-the-virtual-machine-scale-set"></a>Отмена распределения ресурсов для масштабируемого набора виртуальных машин

```java
// deallocate the virtual machine scale set
virtualMachineScaleSet.deallocate();
```

Команда deallocate() завершает работу операционной системы на виртуальных машинах и освобождает вычислительные и сетевые ресурсы (например, IP-адреса), которые использовались экземплярами масштабируемого набора. Плата за хранение на дисках, подключенных к виртуальным машинам (в том числе на дисках с ОС), будет продолжать начисляться.

## <a name="start-a-virtual-machine-scale-set"></a>Запуск масштабируемого набора виртуальных машин

```java
// start a deallocated or stopped virtual machine scale set
virtualMachineScaleSet.start();
```

## <a name="update-the-number-of-virtual-machines-instances-in-the-scale-set"></a>Обновление количества экземпляров виртуальных машин в масштабируемом наборе
```java
// increase the number of virtual machine scale set instances from three to six
virtualMachineScaleSet.update()
                    .withCapacity(6)
                    .apply();
```

Чтобы изменить количество виртуальных машин в масштабируемом наборе, используйте команду `withCapacity()`. Масштабировать емкость каждой виртуальной машины можно с помощью команды `withSku()`.

## <a name="sample-explanation"></a>Описание примера

[Пример кода](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java) сначала создает виртуальную сеть для масштабируемого набора для обмена данными и подсистему балансировки нагрузки для распределения трафика между виртуальными машинами. Цепочка методов `azure.virtualMachineScaleSets().define()...create()` создает масштабируемый набор с тремя экземплярами Linux, которые работают на веб-сервере Apache.    
   
| Класс, используемый в примере | Примечания
|-------|-------|
| [VirtualMachineScaleSet](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._virtual_machine_scale_set) | Запуск, остановка, обновление и удаление всех виртуальных машин в масштабируемом наборе, а также выполнение запросов к ним.
| [VirtualMachineScaleSetVM](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._virtual_machine_scale_set_v_m) | Класс получен из `virtualMachineScaleSet.virtualMachines().get()` или `list()`. Он позволяет запускать, останавливать, настраивать и удалять виртуальные машины в масштабируемом наборе, а также выполнять запросы к ним.
| [VirtualMachineScaleSetNetworkInterface](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network._virtual_machine_scale_set_network_interface) | Класс возвращен из `virtualMachineScaleSet.listNetworkInterfaces()`. Представление только для чтения сетевого интерфейса на виртуальной машине в масштабируемом наборе.
| [VirtualMachineScaleSetSkuTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute._virtual_machine_scale_set_sku_types) | Класс статических полей, используемый для задания [уровня масштабируемого набора виртуальных машин](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/). Этот уровень определяет количество ресурсов, которые могут использовать элементы масштабируемого набора.
| [VirtualMachineScaleSetNicIpConfiguration](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network._virtual_machine_scale_set_nic_i_p_configuration) | Используется для запроса IP-конфигурации, связанной с сетевым интерфейсом в масштабируемом наборе виртуальных машин.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [next-steps](includes/java-next-steps.md)]