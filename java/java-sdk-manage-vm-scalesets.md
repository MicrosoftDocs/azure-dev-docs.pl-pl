---
title: Zarządzanie zestawami skalowania maszyn wirtualnych przy użyciu języka Java | Microsoft Docs
description: Przykładowy kod umożliwiający zarządzanie zestawami skalowania maszyn wirtualnych platformy Azure przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: b55923b7-d60a-460d-b77c-af5fac67f1cc
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 092bb328c4d7e68da9c75a43eaa9c31173d79864
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002538"
---
# <a name="manage-azure-virtual-machine-scale-sets-from-your-java-applications"></a>Zarządzanie zestawami skalowania maszyn wirtualnych platformy Azure z poziomu aplikacji Java

W [tym przykładzie](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets) tworzony jest [zestaw skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) przy użyciu [bibliotek zarządzania Java](https://github.com/Azure/azure-sdk-for-java). 

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets.git
cd compute-java-manage-virtual-machine-scale-sets
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-virtual-network-for-the-scale-set"></a>Tworzenie sieci wirtualnej dla zestawu skalowania

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

Przed utworzeniem definicji zestawu skalowania skonfiguruj sieć wirtualną i moduł równoważenia obciążenia. Zasoby te są używane do początkowej konfiguracji zestawu skalowania.

## <a name="create-a-load-balancer-to-distribute-load-across-the-scale-set"></a>Tworzenie modułu równoważenia obciążenia w celu dystrybucji obciążenia w zestawie skalowania

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

 Moduł równoważenia obciążenia definiuje dwie pule adresów sieciowych zaplecza — do równoważenia obciążenia w protokołach HTTP (`backendPoolName1`) i HTTPS (`backendPoolName2`).  Metody `defineHttpProbe()` umożliwiają skonfigurowanie [punktów końcowych sondy kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) w modułach równoważenia obciążenia. Za pomocą reguł NAT porty 22 i 23 maszyn wirtualnych w zestawie skalowania są uwidaczniane na potrzeby dostępu za pośrednictwem usługi telnet i protokołu SSH.

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
 
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

Za pomocą definicji sieci wirtualnej i definicji modułów równoważenia obciążenia utworzonych w poprzednim kroku utwórz zestaw skalowania z trzema wystąpieniami systemu Linux (`withCapacity(3)`) i trzema dyskami danych o pojemności 100 GB. W łańcuchu metod `defineNewExtension()` na każdej maszynie wirtualnej instalowany jest serwer internetowy Apache.

## <a name="list-virtual-machine-scale-set-network-interfaces"></a>Wyświetlanie listy interfejsów sieciowych zestawu skalowania maszyn wirtualnych

```java
// List network interfaces on the scale set and iterate through them
PagedList<VirtualMachineScaleSetNetworkInterface> vmssNics = 
     virtualMachineScaleSet.listNetworkInterfaces();
for (VirtualMachineScaleSetNetworkInterface vmssNic : vmssNics) {
    System.out.println(vmssNic.id());
}
```

## <a name="get-ssh-connection-strings-for-each-scale-set-virtual-machine"></a>Pobieranie parametrów połączenia SSH dla każdej maszyny wirtualnej w zestawie skalowania

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

W utworzonej wcześniej puli NAT porty SSH i Telnet (odpowiednio 22 i 23) na maszynach wirtualnych zostały zamapowane na porty w module równoważenia obciążenia. W przedstawionym kodzie są tworzone parametry połączenia SSH dla każdej maszyny wirtualnej.

## <a name="stop-the-virtual-machine-scale-set"></a>Zatrzymywanie zestawu skalowania maszyn wirtualnych

```java
// stop (not deallocate) all scale set instances
virtualMachineScaleSet.powerOff();
```

Zatrzymane maszyny wirtualne nadal zużywają zarezerwowane zasoby. Za pomocą metody `deallocate()` zatrzymaj system operacyjny na maszynach wirtualnych, aby zwolnić zasoby obliczeniowe.

## <a name="deallocate-the-virtual-machine-scale-set"></a>Cofanie przydziału zestawu skalowania maszyn wirtualnych

```java
// deallocate the virtual machine scale set
virtualMachineScaleSet.deallocate();
```

Metoda Deallocate () zamyka system operacyjny na maszynach wirtualnych i zwalnia zasoby obliczeniowe oraz sieciowe (takie jak adresy IP) używane przez wystąpienia zestawu skalowania. Nadal są naliczane opłaty za magazyn — wszystkie dyski (w tym dyski systemu operacyjnego) dołączone do maszyn wirtualnych.

## <a name="start-a-virtual-machine-scale-set"></a>Uruchamianie zestawu skalowania maszyn wirtualnych

```java
// start a deallocated or stopped virtual machine scale set
virtualMachineScaleSet.start();
```

## <a name="update-the-number-of-virtual-machines-instances-in-the-scale-set"></a>Aktualizowanie liczby wystąpień maszyn wirtualnych w zestawie skalowania
```java
// increase the number of virtual machine scale set instances from three to six
virtualMachineScaleSet.update()
                    .withCapacity(6)
                    .apply();
```

Metoda `withCapacity()` umożliwia przeskalowanie liczby maszyn wirtualnych w zestawie skalowania. Metoda `withSku()` umożliwia przeskalowanie pojemności poszczególnych maszyn wirtualnych.

## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

[Przykładowy kod](https://github.com/Azure-Samples/compute-java-manage-virtual-machine-scale-sets/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachineScaleSet.java) najpierw tworzy sieć wirtualną dla zestawu skalowania, umożliwiającą komunikację, oraz moduł równoważenia obciążenia, który pozwala dystrybuować ruch między maszynami wirtualnymi. Łańcuch metod `azure.virtualMachineScaleSets().define()...create()` tworzy zestaw skalowania z trzema wystąpieniami systemu Linux, na których jest uruchomiony serwer internetowy Apache.    
   
| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [VirtualMachineScaleSet](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachinescaleset) | Umożliwia uruchamianie, zatrzymywanie, aktualizowanie i usuwanie wszystkich maszyn wirtualnych w zestawie skalowania oraz wykonywanie na nich zapytań.
| [VirtualMachineScaleSetVM](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachinescalesetvm) | Jest pobierana za pomocą metody `virtualMachineScaleSet.virtualMachines().get()` lub `list()`. Umożliwia uruchamianie, zatrzymywanie, konfigurowanie i usuwanie maszyn wirtualnych w zestawie skalowania oraz wykonywanie na nich zapytań.
| [VirtualMachineScaleSetNetworkInterface](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.virtualmachinescalesetnetworkinterface) | Reprezentacja interfejsu sieciowego tylko do odczytu na maszynie wirtualnej w zestawie skalowania, zwracana prze metodę `virtualMachineScaleSet.listNetworkInterfaces()`.
| [VirtualMachineScaleSetSkuTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachinescalesetskutypes) | Klasa pól statycznych służąca do ustawiania [warstwy zestawu skalowania maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/), która określa, ile zasobów mogą zużywać elementy członkowskie zestawu skalowania.
| [VirtualMachineScaleSetNicIpConfiguration](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.virtualmachinescalesetnicipconfiguration) | Służy do wykonywania zapytania o konfigurację protokołu IP skojarzoną z interfejsem sieciowym na maszynie wirtualnej w zestawie skalowania.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]