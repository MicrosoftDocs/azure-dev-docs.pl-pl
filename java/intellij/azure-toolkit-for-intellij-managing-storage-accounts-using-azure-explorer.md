---
title: Zarządzanie kontami magazynu przy użyciu programu Azure Explorer for IntelliJ
description: Dowiedz się, jak zarządzać kontami magazynu platformy Azure za pomocą programu Azure Explorer for IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: bd5c6960f9fe5c5c4d3b1e4b639ce68d887917ad
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999487"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-intellij"></a>Zarządzanie kontami magazynu przy użyciu programu Azure Explorer for IntelliJ

Program Azure Explorer będący częścią zestawu Azure Toolkit for IntelliJ udostępnia deweloperom języka Java łatwe w użyciu rozwiązanie do zarządzania kontami magazynu na koncie platformy Azure z poziomu zintegrowanego środowiska projektowego (IDE) platformy IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-intellij"></a>Tworzenie konta magazynu na platformie IntelliJ

Aby utworzyć konto magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. Zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w temacie [Sign-in instructions for the Azure Toolkit for IntelliJ] (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for IntelliJ). 

2. W widoku programu **Azure Explorer** rozwiń węzeł **Azure** kliknij prawym przyciskiem myszy pozycję **Storage Accounts** (Konta magazynu), a następnie kliknij pozycję **Create Storage Account** (Utwórz konto magazynu).

   ![Polecenie Create Storage Account (Utwórz konto magazynu)][CS01]

3. W oknie dialogowym **Create Storage Account** (Tworzenie konta magazynu) określ następujące opcje:

   ![Okno dialogowe tworzenia nowego konta magazynu][CS02]

   * **Nazwa**: określa nazwę nowego konta magazynu.

   * **Account kind** (Rodzaj konta): określa typ konta magazynu do utworzenia (na przykład „Blob Storage”). Aby uzyskać więcej informacji, zobacz [Informacje o kontach magazynu platformy Azure]. 

   * **Wydajność**: określa, której oferty konta magazynu od wybranego wydawcy należy użyć (na przykład „Premium”). Aby uzyskać więcej informacji, zobacz [Cele usługi Azure Storage dotyczące skalowalności i wydajności]. 

   * **Replication** (Replikacja): określa replikację konta magazynu (na przykład „Strefowo nadmiarowy”). Aby uzyskać więcej informacji, zobacz temat [Replikacja magazynu platformy Azure]. 

   * **Subskrypcja**: określa subskrypcję platformy Azure, która ma być używana dla nowego konta magazynu.

   * **Lokalizacja**: określa lokalizację, w której zostanie utworzone konto magazynu (na przykład „Zachodnie stany USA”).

   * **Grupa zasobów**: określa grupę zasobów dla maszyny wirtualnej. Wybierz jedną z następujących opcji:
      * **Utwórz nową**: wskazuje, że chcesz utworzyć nową grupę zasobów.
      * **Use Existing** (Użyj istniejącej): wskazuje, że wybierzesz grupę z listy grup zasobów skojarzonych z kontem platformy Azure.

4. Po określeniu wartości wszystkich opcji kliknij przycisk **OK**.

## <a name="create-a-storage-container-in-intellij"></a>Tworzenie kontenera magazynu na platformie IntelliJ

Aby utworzyć kontener magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu Azure Explorer kliknij prawym przyciskiem myszy konto magazynu, w którym chcesz utworzyć kontener, a następnie kliknij pozycję **Create blob container** (Utwórz kontener obiektów blob).

   ![Polecenie Create blob container (Utwórz kontener obiektów blob)][CC01]

2. W oknie dialogowym **Create blob container** (Tworzenie kontenera obiektów blob) określ nazwę kontenera, a następnie kliknij przycisk **OK**. Aby uzyskać dodatkowe informacje o nazewnictwie kontenerów, zobacz temat [Naming and referencing containers, blobs, and metadata] (Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych).

   ![Okno dialogowe tworzenia kontenera magazynu][CC02]

## <a name="delete-a-storage-container-in-intellij"></a>Usuwanie kontenera magazynu na platformie IntelliJ

Aby usunąć kontener magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu Azure Explorer kliknij prawym przyciskiem myszy kontener magazynu, a następnie kliknij pozycję **Delete** (Usuń).

   ![Polecenie usuwania kontenera magazynu][DC01]

2. W oknie potwierdzenia kliknij przycisk **Yes** (Tak).

   ![Okno potwierdzania usuwania kontenera magazynu][DC02]

## <a name="delete-a-storage-account-in-intellij"></a>Usuwanie konta magazynu na platformie IntelliJ

Aby usunąć konto magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy kontener magazynu, a następnie kliknij pozycję **Delete** (Usuń).

   ![Menu usuwania konta magazynu][DS01]

2. W oknie potwierdzenia kliknij przycisk **Yes** (Tak).

   ![Okno potwierdzania usuwania konta magazynu][DS02]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kont magazynu Azure, zapoznaj się z następującymi zasobami:

* [Wprowadzenie do usługi Microsoft Azure Storage]
* [Informacje o kontach magazynu platformy Azure]
* Rozmiary kont magazynu platformy Azure
  * [Rozmiary kont magazynu dla systemu Windows na platformie Azure]
  * [Rozmiary kont magazynu dla systemu Linux na platformie Azure]
* Ceny kont magazynu platformy Azure
  * [Ceny kont magazynu w systemie Windows]
  * [Ceny kont magazynu w systemie Linux]

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for IntelliJ)
[Wprowadzenie do usługi Microsoft Azure Storage]: /azure/storage/storage-introduction
[Informacje o kontach magazynu platformy Azure]: /azure/storage/storage-create-storage-account
[Replikacja magazynu platformy Azure]: /azure/storage/storage-redundancy
[Cele usługi Azure Storage dotyczące skalowalności i wydajności]: /azure/storage/storage-scalability-targets
[Naming and referencing containers, blobs, and metadata]: https://go.microsoft.com/fwlink/?LinkId=255555 (Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych)

[Rozmiary kont magazynu dla systemu Windows na platformie Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Rozmiary kont magazynu dla systemu Linux na platformie Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Ceny kont magazynu w systemie Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Ceny kont magazynu w systemie Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png
