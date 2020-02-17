---
title: Zarządzanie maszynami wirtualnymi platformy Azure za pomocą języka Java | Microsoft Docs
description: Przykładowy kod umożliwiający zarządzanie maszynami wirtualnymi platformy Azure przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: 88629aee-6279-433e-a08b-4f8e290446d0
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: c1b8145fd2e7085f524329f958c43797df6c6b1a
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002416"
---
# <a name="manage-azure-virtual-machines-from-your-java-applications"></a>Zarządzanie maszynami wirtualnymi platformy Azure z poziomu aplikacji Java

[Ten przykład](https://github.com/Azure-Samples/compute-java-manage-vm/) przedstawia tworzenie maszyn wirtualnych platformy Azure i korzystanie z nich przy użyciu [bibliotek zarządzania Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/compute-java-manage-vm.git
cd compute-java-manage-vm
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-windows-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Windows

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

Ten kod:   

0. Definiuje obiekt Creatable `Disk` o rozmiarze 50 GB i losowej nazwie, który będzie używany z maszyną wirtualną.
0. Tworzy maszynę wirtualną z systemem Windows Server 2012 za pomocą łańcucha `azure.virtualMachines().define()..create()`. Interfejs API tworzy zdefiniowany w poprzednim kroku obiekt `Disk` w tym samym czasie co maszynę wirtualną. Za pomocą metody `withNewDataDisk(10)` do maszyny wirtualnej dołączany jest również dysk danych 10 GB.

Dowiedz się więcej o korzystaniu z [obiektów<T> Creatable](java-sdk-azure-concepts.md#Creatables), które umożliwiają definiowanie lokalnych reprezentacji zasobów i tworzenie ich wtedy, gdy są wymagane przez inne zasoby platformy Azure.

## <a name="stop-start-and-restart-a-virtual-machine"></a>Zatrzymywanie, uruchamianie i ponowne uruchamianie maszyny wirtualnej

```java
// look up a virtual machine by its ID and then restart, stop, and start it
azureVM = azure.getVirtualMachine.getById(windowsVM.id());

azureVM.restart();
azureVM.powerOff();
azureVM.start();
```

Metoda `powerOff()` wyłącza system operacyjny maszyny wirtualnej, ale nie cofa przydziału zasobów.

## <a name="add-a-virtual-machine-to-an-existing-network"></a>Dodawanie maszyny wirtualnej do istniejącej sieci

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

Użyj metody `withPopularLinuxImage`, aby zdefiniować maszynę wirtualną z systemem Linux, a nie systemem Windows.


## <a name="list-virtual-machines"></a>Wyświetlanie listy maszyn wirtualnych

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

Wyświetl listę wszystkich maszyn wirtualnych w subskrypcji przy użyciu metody `azure.virtualMachines().list()` i wykonaj iteracje na mapie zwróconej przez metodę `tags()`, aby zarządzać otagowanymi kolekcjami maszyn wirtualnych w grupach zasobów.

## <a name="update-a-virtual-machine"></a>Aktualizowanie maszyny wirtualnej

```java
// add a 10GB data disk to the virtual machine
windowsVM.update()
     .withNewDataDisk(10)
     .apply();
```

Aby zaktualizować konfigurację maszyny wirtualnej, użyj łańcucha `update()...apply()` oraz metod używanych do konfigurowania maszyny wirtualnej utworzonej za pośrednictwem łańcucha `define()...create()`.

## <a name="delete-a-virtual-machine"></a>Usuwanie maszyny wirtualnej

```java
// delete by ID if you already are working with the VM object
azure.virtualMachines().deleteById(windowsVM.id());

// delete by resource group and name
azure.virtualMachines().deleteByResourceGroup(rgName,windowsVmName);
```

## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

[Przykładowy kod](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java) tworzy maszynę wirtualną z systemem Windows z dyskiem danych 50 GB. Następnie jest tworzony drugi dysk danych 10 GB, który jest dołączany do maszyny wirtualnej z systemem Windows.
W dalszej kolejności przykładowy kod tworzy maszynę wirtualną z systemem Linux w tej samej sieci wirtualnej, w której znajduje się maszyna wirtualna z systemem Windows.

Przykładowy kod rejestruje informacje o obu maszynach wirtualnych i usuwa te maszyny przed zakończeniem działania.

| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [VirtualMachine](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachine) | Umożliwia wykonywanie zapytań na właściwościach i zarządzanie stanem maszyn wirtualnych. Pobierana w postaci listy za pomocą metody `azure.virtualMachines().list()` lub według nazwy bądź identyfikatora za pomocą metody `azure.virtualMachines().getByResourceGroup()`.
| [VirtualMachineSizeTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | Klasa z wartościami statycznymi mapowanymi na [opcje rozmiaru maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), które są używane przez metodę `withSize()` do definiowania zasobów przydzielonych do maszyny wirtualnej.
| [Disk](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.disk) | Umożliwia utworzenie dysku do przechowywania danych za pomocą metody `withData()` albo obrazu systemu operacyjnego za pomocą metody `withLinux` lub `withWindows` używanej podczas definiowania dysku. Dyski mogą być dołączane do maszyn wirtualnych w momencie utworzenia (za pomocą metody `using withNewDataDisk` lub `withExistingDataDisk`) albo po utworzeniu (za pomocą łańcucha `update()..apply()` wywoływanego na obiekcie VirtualMachine).
| [DiskSkuTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.diskskutypes) | Klasa z wartościami statycznymi służącymi do definiowania dysku w planie magazynu w warstwie Standardowa lub [Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage).
| [KnownLinuxVirtualMachineImage](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | Klasa z zestawem opcji maszyn wirtualnych z systemem Linux, używana z metodą `withPopularLinuxImage()` podczas definiowania maszyny wirtualnej.
| [KnownWindowsVirtualMachineImage](https://docs.microsoft.com/java/api/com.microsoft.azure.management.compute.knownwindowsvirtualmachineimage) | Klasa z zestawem opcji obrazów maszyn wirtualnych z systemem Windows, używana z metodą `withPopularWindowsImage()` podczas definiowania maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]