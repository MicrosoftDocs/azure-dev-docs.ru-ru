---
title: Управление виртуальными сетями Azure с помощью Java | Документация Майкрософт
description: Пример кода для управления виртуальными сетями Azure из кода Java
author: rloutlaw
ms.assetid: 92736911-3df6-46e7-b751-25bb36bf89b9
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: a048cded2659c1cee15b2e26a5f3c80df79e895c
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82105005"
---
# <a name="create-and-manage-azure-virtual-networks-from-your-java-apps"></a>Создание виртуальных сетей Azure и управление ими с помощью приложений Java

[Этот пример](https://github.com/Azure-Samples/network-java-manage-virtual-network) создает [виртуальную сеть](/azure/virtual-network/virtual-networks-overview) для изоляции ресурсов Azure в сегменте сети, которым вы управляете.

## <a name="run-the-sample"></a>Запуск примера

Создайте [файл проверки подлинности](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file), задайте переменную среды `AZURE_AUTH_LOCATION` и укажите полный путь к файлу на компьютере. Далее выполните:

```
git clone https://github.com/Azure-Samples/network-java-manage-virtual-network.git
cd network-java-manage-virtual-network
mvn clean compile exec:java
```

Просмотрите [полный пример кода на GitHub](https://github.com/Azure-Samples/network-java-manage-virtual-network/blob/master/src/main/java/com/microsoft/azure/management/network/samples/ManageVirtualNetwork.java).

## <a name="authenticate-with-azure"></a>Проверка подлинности с помощью Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-network-security-group-to-block-internet-traffic"></a>Создание группы безопасности сети для блокирования интернет-трафика

```java
// this NSG definition block traffic to and from the public Internet
NetworkSecurityGroup backEndSubnetNsg = azure.networkSecurityGroups()
              .define(vnet1BackEndSubnetNsgName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .defineRule("DenyInternetInComing")
                        .denyInbound()
                        .fromAddress("INTERNET")
                        .fromAnyPort()
                        .toAnyAddress()
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .defineRule("DenyInternetOutGoing")
                        .denyOutbound()
                        .fromAnyAddress()
                        .fromAnyPort()
                        .toAddress("INTERNET")
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .create();
```

Это [правило безопасности сети](/azure/virtual-network/virtual-networks-nsg) блокирует входящий и исходящий интернет-трафик. Эта группа безопасности сети не будет действовать, пока вы не примените ее к подсети в виртуальной сети.

## <a name="create-a-virtual-network-with-two-subnets"></a>Создание виртуальной сети с двумя подсетями

```java
// create the a virtual network with two subnets
// assign the backend subnet a rule blocking public internet traffic
Network virtualNetwork1 = azure.networks().define(vnetName1)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .withAddressSpace("192.168.0.0/16")
                    .withSubnet(vnet1FrontEndSubnetName, "192.168.1.0/24")
                    .defineSubnet(vnet1BackEndSubnetName)
                        .withAddressPrefix("192.168.2.0/24")
                        .withExistingNetworkSecurityGroup(backEndSubnetNsg)
                        .attach()
                    .create();
```

Внутренняя подсеть запрещает доступ из Интернета, используя следующий набор правил в группе безопасности сети. Внешняя подсеть использует [правила по умолчанию](/azure/virtual-network/virtual-networks-nsg), которые разрешают исходящий трафик в Интернет.

## <a name="create-a-network-security-group-to-allow-inbound-http-traffic"></a>Создание группы безопасности сети, разрешающей входящий трафик HTTP
```java
// create a rule that allows inbound HTTP and blocks outbound Internet traffic
NetworkSecurityGroup frontEndSubnetNsg = azure.networkSecurityGroups()
               .define(vnet1FrontEndSubnetNsgName)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .defineRule("AllowHttpInComing")
                        .allowInbound()
                        .fromAddress("INTERNET")
                        .fromAnyPort()
                        .toAnyAddress()
                        .toPort(80)
                        .withProtocol(SecurityRuleProtocol.TCP)
                        .attach()
                    .defineRule("DenyInternetOutGoing")
                        .denyOutbound()
                        .fromAnyAddress()
                        .fromAnyPort()
                        .toAddress("INTERNET")
                        .toAnyPort()
                        .withAnyProtocol()
                        .attach()
                    .create();
```

Это правило безопасности сети открывает входящий трафик через порт 80 из общедоступного Интернета и блокирует весь исходящий трафик из сети в Интернет. 

## <a name="update-a-virtual-network"></a>Обновление виртуальной сети
```java
// update the front end subnet to use the rules in the new network security group
virtualNetwork1.update()
          .updateSubnet(vnet1FrontEndSubnetName)
          .withExistingNetworkSecurityGroup(frontEndSubnetNsg)
          .parent()
          .apply();
```

Обновите внешнюю подсеть, чтобы разрешить входящий трафик HTTP, используя правило безопасности сети, созданное на предыдущем шаге.

## <a name="create-a-virtual-machine-on-a-subnet"></a>Создание виртуальной машины в подсети
```java
// attach the new VM to the front end subnet on the virtual network
VirtualMachine frontEndVM = azure.virtualMachines().define(frontEndVmName)
                    .withRegion(Region.US_EAST)
                    .withExistingResourceGroup(rgName)
                    .withExistingPrimaryNetwork(virtualNetwork1) 
                    .withSubnet(vnet1FrontEndSubnetName)
                    .withPrimaryPrivateIpAddressDynamic()
                    .withNewPrimaryPublicIpAddress(publicIpAddressLeafDnsForFrontEndVm)
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();
```

Параметры `withExistingPrimaryNetwork()` и `withSubnet()` настраивают виртуальную машину для использования внешней подсети в виртуальной сети, созданной на предыдущих шагах.

## <a name="list-virtual-networks-in-a-resource-group"></a>Получение списка виртуальных сетей в группе ресурсов
```java
// iterate over every virtual network in the resource group 
for (Network virtualNetwork : azure.networks().listByResourceGroup(rgName)) {
    // for each subnet on the virtual network, log the network address prefix 
    for (Map.Entry<String, Subnet> entry : virtualNetwork.subnets().entrySet()) {
        String subnetName = entry.getKey();
        Subnet subnet = entry.getValue();
        System.out.println("Address prefix for subnet " + subnetName + 
             " is " + subnet.addressPrefix());
    }
}
```       

Вы можете получить список объектов `Network` и проверить их с помощью внешней коллекции или выполнить итерацию каждого дочернего ресурса для каждой сети с помощью вложенного цикла, как показано в этом примере.

## <a name="delete-a-virtual-network"></a>Удаление виртуальной сети
```java
// if you already have the virtual network object it is easiest to delete by ID
azure.networks().deleteById(virtualNetwork1.id());

// Delete by resource group and name if you don't have the VirtualMachine object
azure.networks().deleteByResourceGroup(rgName,vnetName1);
```

Если удалить виртуальную сеть, удаляются также подсети внутри нее, но правила группы безопасности сети, применяемые к подсетям, не удаляются. Эти определения можно повторно применить к другим подсетям.

## <a name="sample-explanation"></a>Описание примера

Этот пример создает виртуальную сеть с двумя подсетями и по одной виртуальной машине в каждой подсети. Внутренняя подсеть изолирована от общедоступного Интернета. Внешняя подсеть принимает входящий трафик HTTP из Интернета. Обе виртуальные машины в виртуальной сети обмениваются данными с помощью стандартных правил групп безопасности сети.

| Класс, используемый в примере | Примечания
|-------|-------|
| [Network](/java/api/com.microsoft.azure.management.network.network) | Представление локального объекта виртуальной сети, созданной с помощью `azure.networks().define()...create()`. Используйте текучую цепочку `update()...apply()` для обновления существующей виртуальной сети.
| [Подсеть](/java/api/com.microsoft.azure.management.network.subnet) | Создание подсетей в виртуальной сети при определении или обновлении сети с помощью `withSubnet()`. Получение представлений объекта подсети с помощью `Network.subnets().get()` или `Network.subnets().entrySet()`. Эти объекты содержат методы для запроса свойств подсети.
| [NetworkSecurityGroup](/java/api/com.microsoft.azure.management.network.networksecuritygroup) | Создан с помощью текучей цепочки `azure.networkSecurityGroups().define()...create()`. Применяется к подсетям при обновлении или создании подсетей в виртуальной сети. 

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [next-steps](includes/java-next-steps.md)]