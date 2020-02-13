---
title: Zarządzanie maszynami wirtualnymi przy użyciu programu Azure Explorer for IntelliJ
description: Dowiedz się, jak zarządzać maszynami wirtualnymi platformy Azure za pomocą programu Azure Explorer for IntelliJ.
documentationcenter: java
ms.date: 11/13/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 621683630769667591d6194d9dba4824de8a4fcb
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999452"
---
# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-intellij"></a>Zarządzanie maszynami wirtualnymi przy użyciu programu Azure Explorer for IntelliJ

Program Azure Explorer będący częścią zestawu Azure Toolkit for IntelliJ udostępnia deweloperom języka Java łatwe w użyciu rozwiązanie do zarządzania maszynami wirtualnymi na koncie platformy Azure z poziomu zintegrowanego środowiska projektowego (IDE) platformy IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-virtual-machine-in-intellij"></a>Tworzenie maszyny wirtualnej na platformie IntelliJ

Aby utworzyć maszynę wirtualną przy użyciu programu Azure Explorer, wykonaj następujące czynności: 

1. Zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w artykule [Sign-in instructions for the Azure Toolkit for IntelliJ] (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for IntelliJ).

2. W widoku programu **Azure Explorer** rozwiń węzeł **Azure** kliknij prawym przyciskiem myszy pozycję **Virtual Machines** (Maszyny wirtualne), a następnie kliknij pozycję **Create VM** (Utwórz maszynę wirtualną). 

   ![Polecenie Create VM (Utwórz maszynę wirtualną)][CR01]  
    Zostanie otwarty kreator **Create new Virtual Machine** (Tworzenie nowej maszyny wirtualnej).

3. W oknie **Choose a Subscription** (Wybieranie subskrypcji) wybierz subskrypcję, a następnie kliknij przycisk **Next** (Dalej). 

   ![Okno Choose a Subscription (Wybieranie subskrypcji)][CR02]

4. W oknie **Select a Virtual Machine Image** (Wybieranie obrazu maszyny wirtualnej) wprowadź następujące informacje:

   * **Lokalizacja**: określa lokalizację, w której zostanie utworzona maszyna wirtualna (na przykład *Zachodnie stany USA*). 

   * **Recommended image** (Zalecany obraz): określa, że wybierzesz obraz z skróconej listy często używanych obrazów.

   * **Custom image** (Obraz niestandardowy): określa, że wybierzesz obraz niestandardowy, podając następujące informacje:

      * **Publisher** (Wydawca): określa wydawcę, który utworzył obraz do użycia dla maszyny wirtualnej (na przykład *Microsoft*).

      * **Offer** (Oferta): określa, której oferty maszyny wirtualnej od wybranego wydawcy należy użyć (na przykład *JDK*).

      * **Sku** (Jednostka SKU): określa jednostkę składowania zapasu do użycia z wybranej oferty (na przykład *JDK_8*).

      * **Version #** (Nr wersji): określa, która wersja wybranej jednostki SKU ma być używana.

   ![Okno Select a Virtual Machine Image (Wybieranie obrazu maszyny wirtualnej)][CR03]

5. Kliknij przycisk **Dalej**. 

6. W oknie **Virtual Machine Basic Settings** (Podstawowe ustawienia maszyny wirtualnej) wprowadź następujące informacje:

   * **Nazwa maszyny wirtualnej**: określa nazwę nowej maszyny wirtualnej, która musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.

   * **Rozmiar**: określa liczbę rdzeni i pamięć do przydzielenia dla maszyny wirtualnej.

   * **User name** (Nazwa użytkownika): określa konto administratora, które ma zostać utworzone w celu zarządzania maszyną wirtualną.

   * **Password** (Hasło) i **Confirm** (Potwierdź): określa hasło dla konta administratora.

   ![Okno Virtual Machine Basic Settings (Podstawowe ustawienia maszyny wirtualnej)][CR04]

7. Kliknij przycisk **Dalej**. 

8. W oknie **Associated Resources** (Skojarzone zasoby) wprowadź następujące informacje:

   * **Grupa zasobów**: określa grupę zasobów dla maszyny wirtualnej. Wybierz jedną z następujących opcji:
      * **Utwórz nową**: wskazuje, że chcesz utworzyć nową grupę zasobów.
      * **Use Existing** (Użyj istniejącej): wskazuje, że chcesz wybrać grupę z listy grup zasobów skojarzonych z kontem platformy Azure.

       ![Okno Associated Resources (Skojarzone zasoby)][CR07]

   * **Konto magazynu**: określa konto magazynu używane do przechowywania maszyny wirtualnej. Możesz użyć istniejącego konta magazynu lub utworzyć nowe konto. W przypadku wybrania pozycji **Create New** (Utwórz nową) zostanie wyświetlone następujące okno dialogowe:

      ![Okno dialogowe Create Storage Account (Tworzenie konta magazynu)][CR05]

   * **Virtual Network** (Sieć wirtualna) i **Subnet** (Podsieć): określa sieć wirtualną i podsieć, z którą połączy się maszyna wirtualna. Możesz użyć istniejącej sieci i podsieci lub utworzyć nową sieć i podsieć. W przypadku wybrania pozycji **Create new** (Utwórz nową) zostanie wyświetlone następujące okno dialogowe:

      ![Okno dialogowe Create Virtual Network (Tworzenie sieci wirtualnej)][CR06]

   * **Publiczny adres IP**: określa zewnętrzny adres IP maszyny wirtualnej. Możesz utworzyć nowy adres IP lub, jeśli maszyna wirtualna nie ma publicznego adresu IP, możesz wybrać opcję **(None)** (Brak). 

   * **Network security group** (Sieciowa grupa zabezpieczeń): określa opcjonalną zaporę sieciową dla maszyny wirtualnej. Możesz wybrać istniejącą zaporę lub, jeśli maszyna wirtualna nie będzie używać zapory sieciowej, możesz wybrać opcję **(None)** (Brak). 

   * **Zestaw dostępności**: określa opcjonalny zestaw dostępności, do którego może należeć maszyna wirtualna. Możesz wybrać istniejący zestaw dostępności, utworzyć nowy zestaw dostępności lub, jeśli maszyna wirtualna nie będzie należeć do zestawu dostępności, wybrać opcję **(None)** (Brak).

9. Kliknij przycisk **Zakończ**.  
    Nowa maszyna wirtualna zostanie wyświetlona w oknie narzędzia Azure Explorer. 

   ![Nowa maszyna wirtualna w widoku programu Azure Explorer][CR08]

## <a name="restart-a-virtual-machine-in-intellij"></a>Ponowne uruchamianie maszyny wirtualnej na platformie IntelliJ

Aby ponownie uruchomić maszynę wirtualną przy użyciu programu Azure Explorer na platformie IntelliJ, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Restart** (Uruchom ponownie).

   ![Polecenie Restart (Uruchom ponownie) dla maszyny wirtualnej][RE01]

2. W oknie potwierdzenia kliknij przycisk **Yes** (Tak). 

   ![Okno potwierdzania ponownego uruchomienia maszyny wirtualnej][RE02]

## <a name="shut-down-a-virtual-machine-in-intellij"></a>Zamykanie maszyny wirtualnej na platformie IntelliJ

Aby zamknąć działającą maszynę wirtualną przy użyciu programu Azure Explorer na platformie IntelliJ, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Shutdown** (Zamknij).

   ![Polecenie Shutdown (Zamknij) dla maszyny wirtualnej][SH01]

2. W oknie potwierdzenia kliknij przycisk **Yes** (Tak). 

   ![Okno potwierdzania zamknięcia maszyny wirtualnej][SH02]

## <a name="delete-a-virtual-machine-in-intellij"></a>Usuwanie maszyny wirtualnej na platformie IntelliJ

Aby usunąć maszynę wirtualną przy użyciu programu Azure Explorer na platformie IntelliJ, wykonaj następujące czynności:

1. W widoku programu **Azure Explorer** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Delete** (Usuń).

   ![Polecenie Delete (Usuń) dla maszyny wirtualnej][DE01]

2. W oknie potwierdzenia kliknij przycisk **Yes** (Tak). 

   ![Okno potwierdzania usunięcia maszyny wirtualnej][DE02]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozmiarów i cen maszyn wirtualnych platformy Azure, zapoznaj się z następującymi zasobami:

* Rozmiary maszyn wirtualnych platformy Azure
  * [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure]
  * [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure]
* Ceny maszyn wirtualnych platformy Azure
  * [Ceny maszyn wirtualnych z systemem Windows]
  * [Ceny maszyn wirtualnych z systemem Linux]

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for IntelliJ)
[Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Ceny maszyn wirtualnych z systemem Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Ceny maszyn wirtualnych z systemem Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR08.png
