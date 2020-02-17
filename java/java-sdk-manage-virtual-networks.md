---
title: Zarządzanie sieciami wirtualnymi platformy Azure za pomocą języka Java | Microsoft Docs
description: Przykładowy kod umożliwiający zarządzanie sieciami wirtualnymi platformy Azure
author: rloutlaw
ms.assetid: 92736911-3df6-46e7-b751-25bb36bf89b9
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 3c537d7d7030ea46bdbc7d6873819ea8e12f03b3
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002362"
---
# <a name="create-and-manage-azure-virtual-networks-from-your-java-apps"></a>Tworzenie sieci wirtualnych platformy Azure i zarządzanie nimi z poziomu aplikacji Java

W [tym przykładzie](https://github.com/Azure-Samples/network-java-manage-virtual-network) tworzona jest [sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) w celu odizolowania zasobów platformy Azure w kontrolowanym segmencie sieciowym.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/network-java-manage-virtual-network.git
cd network-java-manage-virtual-network
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/network-java-manage-virtual-network/blob/master/src/main/java/com/microsoft/azure/management/network/samples/ManageVirtualNetwork.java).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-network-security-group-to-block-internet-traffic"></a>Tworzenie sieciowej grupy zabezpieczeń w celu zablokowania ruchu internetowego

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

Ta [reguła zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) blokuje zarówno przychodzący, jak i wychodzący publiczny ruch internetowy. Sieciowa grupa zabezpieczeń nie będzie działać, dopóki nie zostanie zastosowana do podsieci w sieci wirtualnej.

## <a name="create-a-virtual-network-with-two-subnets"></a>Tworzenie sieci wirtualnej z dwiema podsieciami

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

Podsieć zaplecza uniemożliwia dostęp do Internetu na podstawie zestawu reguł sieciowej grupy zabezpieczeń. Podsieć frontonu używa [reguł domyślnych](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), które zezwalają na ruch wychodzący do Internetu.

## <a name="create-a-network-security-group-to-allow-inbound-http-traffic"></a>Tworzenie sieciowej grupy zabezpieczeń, która zezwala na ruch przychodzący HTTP
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

Ta reguła zabezpieczeń sieci otwiera ruch przychodzący z publicznego Internetu na porcie 80 i blokuje cały ruch wychodzący z sieci do publicznego Internetu. 

## <a name="update-a-virtual-network"></a>Aktualizowanie sieci wirtualnej
```java
// update the front end subnet to use the rules in the new network security group
virtualNetwork1.update()
          .updateSubnet(vnet1FrontEndSubnetName)
          .withExistingNetworkSecurityGroup(frontEndSubnetNsg)
          .parent()
          .apply();
```

Przy użyciu reguły zabezpieczeń sieci utworzonej w poprzednim kroku zaktualizuj podsieć frontonu w taki sposób, aby zezwalała na ruch przychodzący HTTP.

## <a name="create-a-virtual-machine-on-a-subnet"></a>Tworzenie maszyny wirtualnej w podsieci
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

Metody `withExistingPrimaryNetwork()` i `withSubnet()` umożliwiają skonfigurowanie maszyny wirtualnej pod kątem używania podsieci frontonu w sieci wirtualnej utworzonej w poprzednich krokach.

## <a name="list-virtual-networks-in-a-resource-group"></a>Wyświetlanie listy sieci wirtualnych w grupie zasobów
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

Obiekt `Network` można wyświetlić i sprawdzić przy użyciu kolekcji zewnętrznej. Przy użyciu zagnieżdżonej pętli for-each można też wykonać iteracje na poszczególnych zasobach podrzędnych w każdej sieci, jak pokazano w tym przykładzie.

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej
```java
// if you already have the virtual network object it is easiest to delete by ID
azure.networks().deleteById(virtualNetwork1.id());

// Delete by resource group and name if you don't have the VirtualMachine object
azure.networks().deleteByResourceGroup(rgName,vnetName1);
```

Usunięcie sieci wirtualnej powoduje usunięcie podsieci znajdujących się w tej sieci. Jednak reguły sieciowej grupy zabezpieczeń zastosowane do podsieci nie są usuwane. Definicje te można ponownie zastosować do innych podsieci.

## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

W tym przykładzie tworzona jest sieć wirtualna z dwiema podsieciami. W każdej z nich znajduje się jedna maszyna wirtualna. Podsieć zaplecza jest odcięta od publicznego Internetu. Podsieć frontonu akceptuje ruch przychodzący HTTP z Internetu. Maszyny wirtualne w sieci wirtualnej komunikują się ze sobą za pomocą domyślnych reguł sieciowej grupy zabezpieczeń.

| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [Sieć](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.network) | Lokalna reprezentacja obiektu sieci wirtualnej utworzona za pomocą łańcucha `azure.networks().define()...create()`. Aby zaktualizować istniejącą sieć wirtualną, użyj płynnego łańcucha `update()...apply()`.
| [Podsieć](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.subnet) | Umożliwia tworzenie podsieci w sieci wirtualnej podczas definiowania lub aktualizowania sieci przy użyciu metody `withSubnet()`. Pozwala pobierać reprezentacje obiektów podsieci za pomocą łańcucha `Network.subnets().get()` lub `Network.subnets().entrySet()`. Obiekty te udostępniają metody umożliwiające wykonywanie zapytań na właściwościach podsieci.
| [NetworkSecurityGroup](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.networksecuritygroup) | Tworzona za pomocą płynnego łańcucha `azure.networkSecurityGroups().define()...create()`, a następnie stosowana do podsieci podczas aktualizowania lub tworzenia podsieci w sieci wirtualnej. 

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]