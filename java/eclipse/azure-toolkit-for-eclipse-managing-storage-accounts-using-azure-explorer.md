---
title: Zarządzanie kontami magazynu za pomocą programu Azure Explorer for Eclipse
description: Dowiedz się, jak zarządzać kontami magazynu platformy Azure za pomocą programu Azure Explorer for Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 2eee5bf3546712c5f43056a9d4a2b170951ee810
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999515"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Zarządzanie kontami magazynu przy użyciu programu Azure Explorer for Eclipse

Program Azure Explorer, będący częścią zestawu Azure Toolkit for Eclipse, udostępnia deweloperom języka Java łatwe w użyciu rozwiązanie do zarządzania kontami magazynu na koncie platformy Azure z poziomu zintegrowanego środowiska projektowego (IDE) platformy Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Tworzenie konta magazynu na platformie Eclipse

Aby utworzyć konto magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. Zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w temacie [Instrukcje logowania dla zestawu narzędzi Azure Toolkit for Eclipse](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-sign-in-instructions).

1. W widoku programu **Azure Explorer** rozwiń węzeł **Azure**, kliknij prawym przyciskiem myszy pozycję **Konta magazynu**, a następnie kliknij pozycję **Utwórz konto magazynu**.

   ![Polecenie Utwórz konto magazynu][CS01]

1. W oknie dialogowym **Tworzenie konta magazynu** określ następujące opcje:

   ![Okno dialogowe tworzenia nowego konta magazynu][CS02]

   * **Nazwa**: określa nazwę nowego konta magazynu.

   * **Subskrypcja**: określa subskrypcję platformy Azure, która ma być używana dla nowego konta magazynu.

   * **Grupa zasobów**: określa grupę zasobów dla maszyny wirtualnej. Wybierz jedną z następujących opcji:
      * **Utwórz nową**: wskazuje, że chcesz utworzyć nową grupę zasobów.
      * **Użyj istniejącej**: wskazuje, że wybierzesz grupę z listy grup zasobów skojarzonych z kontem platformy Azure.

   * **Region**: określa lokalizację, w której zostanie utworzone konto magazynu (na przykład „Zachodnie stany USA”).

   * **Rodzaj konta**: określa typ konta magazynu do utworzenia (na przykład „Blob Storage”). Aby uzyskać więcej informacji, zobacz [Informacje o kontach magazynu platformy Azure].

   * **Wydajność**: określa, której oferty konta magazynu od wybranego wydawcy należy użyć (na przykład „Premium”). Aby uzyskać więcej informacji, zobacz [Cele usługi Azure Storage dotyczące skalowalności i wydajności].

   * **Replikacja**: określa replikację konta magazynu (na przykład „Strefowo nadmiarowy”). Aby uzyskać więcej informacji, zobacz [Replikacja magazynu platformy Azure].

1. Po określeniu wartości wszystkich opcji kliknij pozycję **Utwórz**.

## <a name="create-a-storage-container-in-eclipse"></a>Tworzenie kontenera magazynu na platformie Eclipse

Aby utworzyć kontener magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy konto magazynu, w którym chcesz utworzyć kontener, a następnie kliknij pozycję **Utwórz kontener obiektów blob**.

   ![Polecenie tworzenia kontenera obiektów blob][CC01]

1. W oknie dialogowym **Tworzenie kontenera obiektów blob** określ nazwę kontenera, a następnie kliknij przycisk **OK**. Aby uzyskać dodatkowe informacje o nazewnictwie kontenerów, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych].

   ![Okno dialogowe Tworzenie kontenera obiektów blob][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Usuwanie kontenera magazynu na platformie Eclipse

Aby usunąć kontener magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy kontener magazynu, a następnie kliknij pozycję **Usuń**.

   ![Polecenie usuwania kontenera magazynu][DC01]

1. W oknie potwierdzenia kliknij przycisk **OK**.

   ![Okno potwierdzania usuwania kontenera magazynu][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Usuwanie konta magazynu na platformie Eclipse

Aby usunąć konto magazynu przy użyciu programu Azure Explorer, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy konto magazynu, a następnie kliknij pozycję **Usuń**.

   ![Polecenie Usuń konto magazynu][DS01]

1. W oknie potwierdzenia kliknij przycisk **OK**.

   ![Okno potwierdzania usuwania konta magazynu][DS02]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kont magazynu platformy Azure, ich rozmiarów i cen, zapoznaj się z następującymi zasobami:

* [Wprowadzenie do usługi Microsoft Azure Storage]
* [Informacje o kontach magazynu platformy Azure]
* Rozmiary kont magazynu platformy Azure
  * [Rozmiary kont magazynu dla systemu Windows na platformie Azure]
  * [Rozmiary kont magazynu dla systemu Linux na platformie Azure]
* Ceny kont magazynu platformy Azure
  * [Ceny kont magazynu w systemie Windows]
  * [Ceny kont magazynu w systemie Linux]

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Wprowadzenie do usługi Microsoft Azure Storage]: /azure/storage/storage-introduction
[Informacje o kontach magazynu platformy Azure]: /azure/storage/storage-create-storage-account
[Replikacja magazynu platformy Azure]: /azure/storage/storage-redundancy
[Cele usługi Azure Storage dotyczące skalowalności i wydajności]: /azure/storage/storage-scalability-targets
[Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych]: https://go.microsoft.com/fwlink/?LinkId=255555

[Rozmiary kont magazynu dla systemu Windows na platformie Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Rozmiary kont magazynu dla systemu Linux na platformie Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Ceny kont magazynu w systemie Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Ceny kont magazynu w systemie Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png
