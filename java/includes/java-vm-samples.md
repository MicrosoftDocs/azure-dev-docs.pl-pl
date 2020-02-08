---
ms.openlocfilehash: 5d005f5a4906e5e2a55189d073383ab77f6c1860
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013722"
---
| **Tworzenie maszyn wirtualnych** || 
|---|---|
| [Zarządzanie maszynami wirtualnymi][1] | Twórz, modyfikuj, uruchamiaj, zatrzymuj i usuwaj maszyny wirtualne. |
| [Tworzenie maszyny wirtualnej za pomocą obrazu niestandardowego][2] | Utwórz niestandardowy obraz maszyny wirtualnej i używaj go do tworzenia nowych maszyn wirtualnych. | 
| [Tworzenie maszyny wirtualnej przy użyciu wyspecjalizowanego wirtualnego dysku twardego z migawki][3] | Utwórz migawkę na podstawie dysków systemu operacyjnego i danych maszyny wirtualnej, utwórz dyski zarządzane na bazie migawki, a następnie utwórz maszynę wirtualną, dołączając dyski zarządzane. |  
| [Równoległe tworzenie maszyn wirtualnych w tej samej sieci][4] | Twórz maszyny wirtualne w tym samym regionie tej samej sieci wirtualnej z dwiema równoległymi podsieciami. |
| [Równoległe tworzenie maszyn wirtualnych w różnych regionach][5] | Utwórz zestaw maszyn wirtualnych w wielu regionach platformy Azure i zrównoważ jego obciążenie. |
| **Sieciowe maszyny wirtualne** || 
| [Zarządzanie sieciami wirtualnymi][6] | Skonfiguruj sieć wirtualną z dwiema podsieciami i ogranicz dostęp do nich przez Internet. |
| **Tworzenie zestawów skalowania** ||
| [Tworzenie zestawu skalowania maszyn wirtualnych z modułem równoważenia obciążenia][7] | Utwórz zestaw skalowania maszyn wirtualnych, skonfiguruj moduł równoważenia obciążenia i uzyskaj parametry połączenia SSH z maszynami wirtualnymi w zestawie skalowania. |

[1]: ../java-sdk-manage-virtual-machines.md
[2]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-custom-image/
[3]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-specialized-disk-from-vhd/
[4]: https://github.com/Azure-Samples/compute-java-manage-virtual-machines-in-parallel/
[5]: ../java-sdk-virtual-machines-in-parallel.md
[6]: ../java-sdk-manage-virtual-networks.md
[7]: ../java-sdk-manage-vm-scalesets.md