---
title: Równoległe tworzenie maszyn wirtualnych w różnych regionach | Microsoft Docs
description: Przykładowy kod umożliwiający równoległe tworzenie maszyn wirtualnych w różnych regionach świadczenia usługi Azure przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: e5a36699-2d96-4571-84f9-a6af13f3c067
ms.topic: article
ms.date: 03/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: ef56241e0ddf0dca34a0229c7d2261d996d05870
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002511"
---
# <a name="create-virtual-machines-across-multiple-regions-from-your-java-applications"></a>Tworzenie maszyn wirtualnych w wielu regionach z poziomu aplikacji Java

[W tym przykładzie](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel) są równolegle tworzone maszyny wirtualne w różnych regionach świadczenia usługi Azure przy użyciu [bibliotek zarządzania platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java).

> [!IMPORTANT]
> W tym przykładzie tworzonych jest łącznie 48 maszyn wirtualnych z systemem Ubuntu 16.04 LTS o rozmiarze [STANDARDOWA_DS3_V2](/azure/virtual-machines/virtual-machines-windows-sizes) w czterech regionach. Przed zamknięciem programu maszyny wirtualne są usuwane w kodzie. Przed uruchomieniem tego przykładu z domyślną liczbą maszyn wirtualnych pamiętaj, aby [sprawdzić ograniczenia oraz limit przydziału usługi](/azure/azure-subscription-service-limits).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel.git
cd compute-java-create-virtual-machines-across-regions-in-parallel
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/CreateVirtualMachinesInParallel.java).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="set-locations-and-counts-for-the-virtual-machines"></a>Ustawianie lokalizacji i liczby maszyn wirtualnych

```java
// use a Map to define how where and how many VMs to create 
Map<Region, Integer> virtualMachinesByLocation = new HashMap<Region, Integer>();

// create 12 virtual machines in four regions
virtualMachinesByLocation.put(Region.US_EAST, 12);
virtualMachinesByLocation.put(Region.US_SOUTH_CENTRAL, 12);
virtualMachinesByLocation.put(Region.US_WEST, 12);
virtualMachinesByLocation.put(Region.US_NORTH_CENTRAL, 12);
```

Element `Map` jest używany w dalszej części przykładu do ustawiania globalnej dystrybucji maszyn wirtualnych.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów 

```java
// logically associate the resources in the sample into a randomly named resource group
final String rgName = SdkContext.randomResourceName("rgCOPD", 24);
ResourceGroup resourceGroup = azure.resourceGroups().define(rgName)
                .withRegion(Region.US_EAST)
                .create();
```

Każdy zasób w przykładzie jest zarządzany przez tę grupę zasobów. Ułatwia to końcowe oczyszczanie zasobów — wystarczy usunąć grupę zasobów.

## <a name="define-the-virtual-machines"></a>Definiowanie maszyn wirtualnych
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

Powyższa zewnętrzna pętla `for` iteruje po poszczególnych regionach, definiując sieć wirtualną typu Creatable i konto magazynu typu Creatable, które będą używane przez wszystkie maszyny wirtualne w tym regionie. Dowiedz się więcej o używaniu obiektów [Creatable](java-sdk-azure-concepts.md#Creatables) umożliwiających tworzenie zasobów dopiero wtedy, gdy są potrzebne (w przypadku korzystania z bibliotek zarządzania).

Wewnętrzna pętla `for` pobiera publiczny adres IP typu Creatable maszyny wirtualnej, a następnie definiuje maszynę wirtualną typu Creatable przy użyciu obiektów Creatable dla zdefiniowanych wcześniej sieci wirtualnej, konta magazynu i publicznego adresu IP.  Ta maszyna wirtualna typu Creatable jest następnie dodawana do listy `creatableVirtualMachines`.

## <a name="create-the-virtual-machines"></a>Tworzenie maszyn wirtualnych

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

Wywołanie `azure.virtualMachines().create(creatableVirtualMachines)` równolegle tworzy wszystkie maszyny wirtualne zdefiniowane na liście `creatableVirtualMachines` w różnych regionach.

Zwrócony obiekt `CreatedResources<VirtualMachine>` zapewnia dostęp do dowolnych zasobów utworzonych w metodzie `create()` w ramach subskrypcji platformy Azure — nie tylko do zwróconego typu `VirtualMachine`. Wykonaj rzutowanie wartości zwróconej przez metodę `createdRelatedResources()` na poprawny typ. 

Więcej informacji na temat korzystania z obiektów `Creatable<T>` i `CreatedResources` można znaleźć w [artykule dotyczącym pojęć związanych z biblioteką](java-sdk-azure-concepts.md).

## <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów 

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

Ten blok pozwala usunąć zasoby utworzone w przykładzie.

## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

Zapoznaj się z kompletnym przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel).

Używane w tym przykładzie obiekty `Creatable` umożliwiają zdefiniowanie sieci wirtualnej i konta magazynu w każdym regionie, w którym są hostowane maszyny wirtualne. Następnie obiekty `Creatable` są definiowane z publicznym adresem IP dla każdej maszyny wirtualnej. Maszyny wirtualne są definiowane za pomocą tych obiektów `Creatable`, a następnie definicje maszyn wirtualnych są dodawane do listy `virtualMachineCreatable`.

Po dodaniu wszystkich definicji maszyn wirtualnych do listy metoda `azure.virtualMachines().create(creatableVirtualMachines)` tworzy równolegle poszczególne maszyny wirtualne na platformie Azure.

W przykładowym kodzie są następnie pobierane adresy IP wszystkich utworzonych maszyn wirtualnych ze zwróconego obiektu CreatedResources w celu utworzenia wystąpienia usługi [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview), która pozwala rozłożyć obciążenie na maszynach wirtualnych. 

Blok `finally` umożliwia usunięcie zasobów z subskrypcji platformy Azure nawet w przypadku wystąpienia błędu.

| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [VirtualMachine](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachine) | Umożliwia wykonywanie zapytań na właściwościach i zarządzanie stanem maszyn wirtualnych. Pobierana w postaci listy za pomocą metody `azure.virtualMachines().list()` lub według nazwy bądź identyfikatora za pomocą metody `azure.virtualMachines().getByResourceGroup()`.
| [VirtualMachineSizeTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | Wartości statyczne mapowane na [opcje rozmiarów maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), które są używane jako parametr metody `withSize()` podczas definiowania maszyny wirtualnej.
| [PublicIpAddress](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.publicipaddress) | Zdefiniowana (ale nie tworzona natychmiast) dla każdej maszyny wirtualnej za pośrednictwem metody `azure.publicIpAddresses().define()`. Umożliwia przechowywanie klucza dla każdego obiektu `Creatable` i pobieranie go za pomocą metody `createdRelatedResource()`.
| [KnownLinuxVirtualMachineImage](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | Zestaw opcji maszyn wirtualnych z systemem Linux używany jako parametr metody `withPopularLinuxImage()` podczas definiowania maszyny wirtualnej.
| [Sieć](https://docs.microsoft.com/java/api/com.microsoft.azure.management.network.network) | W tym przykładzie w każdym regionie definiowana jest jedna sieć wirtualna — za pomocą metody `azure.networks().define()`. 

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]