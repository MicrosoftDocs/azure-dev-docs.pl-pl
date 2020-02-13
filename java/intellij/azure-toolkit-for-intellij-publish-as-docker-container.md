---
title: Publikowanie aplikacji internetowej jako kontenera platformy Docker
titleSuffix: Azure Toolkit for IntelliJ
description: Dowiedz się, jak opublikować aplikację internetową na platformie Microsoft Azure jako kontener platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 8b601f2faf1431cf393db3560077e299d2a52f4c
ms.sourcegitcommit: fc3408b6e153c847dd90026161c4c498aa06e2fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2019
ms.locfileid: "77000383"
---
# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij-deprecated"></a>Publikowanie aplikacji internetowej jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ (PRZESTARZAŁE)

Użycie kontenerów platformy Docker jest powszechnie stosowaną metodą wdrażania aplikacji internetowych. Przy użyciu kontenerów platformy Docker deweloperzy mogą skonsolidować wszystkie pliki projektu i zależności w jednym pakiecie w celu wdrożenia na serwerze. Zestaw narzędzi Azure Toolkit for IntelliJ pozwala uprościć ten proces dla deweloperów języka Java dzięki dodaniu funkcji *Publish as Docker Container* (Publikowanie jako kontener platformy Docker) na potrzeby wdrażania na platformie Microsoft Azure. Ten artykuł zawiera instrukcje publikowania aplikacji na platformie Azure jako kontenerów platformy Docker.

> [!NOTE]
>
> Więcej informacji na temat platformy Docker znajduje się w [Witryna internetowa platformy Docker].
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Publikowanie aplikacji internetowej na platformie Azure przy użyciu kontenera platformy Docker

> [!NOTE]
> * Aby opublikować aplikację internetową, należy utworzyć artefakt gotowy do wdrożenia. Aby dowiedzieć się więcej, zobacz sekcję [Dodatkowe informacje na temat tworzenia artefaktów](#artifacts).
>
> * Jeśli instrukcje kreatora wdrażania zostaną zrealizowane co najmniej raz, większość ustawień zostanie zastosowana jako wartości domyślnie w przypadku ponownego uruchomienia kreatora.
>

1. Otwórz projekt aplikacji internetowej w środowisku IntelliJ.

2. Aby uruchomić kreatora **Publish as Docker Container** (Publikowanie jako kontener platformy Docker), wykonaj jedną z następujących czynności:

   * W oknie **Project** (Projekt) kliknij prawym przyciskiem myszy projekt, kliknij pozycję **Azure**, a następnie kliknij pozycję **Publish as Docker Container** (Publikuj jako kontener platformy Docker):

      ![Polecenie Publish as Docker Container (Publikuj jako kontener platformy Docker)][PUB01]

   * Na pasku narzędzi programu IntelliJ kliknij przycisk **Publish Group** (Publikuj grupę), a następnie kliknij pozycję **Publish as Docker Container** (Publikuj jako kontener platformy Docker):

      ![Polecenie Publish as Docker Container (Publikuj jako kontener platformy Docker)][PUB02]  
    Zostanie otwarty kreator **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure).

   ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB03]

3. W oknie **Type an image name, select the artifact's path and check a Docker host to be used** (Wpisz nazwę obrazu, wybierz ścieżkę artefaktu i sprawdź hosta platformy Docker, który zostanie użyty), wykonaj następujące czynności: 

   a. W polu **Docker image name** (Nazwa obrazu platformy Docker) wprowadź unikatową nazwę hosta platformy Docker. (Kreator automatycznie tworzy nazwę, ale można ją zmodyfikować). 

   b. W obszarze **Hosts** (Hosty) zostaną wyświetlone wszystkie hosty platformy Docker, które zostały już utworzone. Wykonaj jedną z następujących czynności: 
   * Jeśli masz istniejącego hosta platformy Docker, możesz wdrożyć na nim swoją aplikację internetową.
   * Aby utworzyć hosta platformy Docker, kliknij zielony znak plus ( **+** ).  
     Zostanie otwarte okno dialogowe **Create Docker Host** (Tworzenie hosta platformy Docker). 

     ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB04a]

4. W oknie **Configure the new virtual machine** (Konfigurowanie nowej maszyny wirtualnej) podaj następujące informacje dotyczące hosta platformy Docker. (Kreator automatycznie generuje większość informacji, ale można je modyfikować). 

   a. W polu **Name** (Nazwa) wprowadź unikatową nazwę hosta platformy Docker. (Nie jest taka sama jak określona wcześniej nazwa obrazu platformy Docker). 
    
   b. W polu **Subscription** (Subskrypcja) wprowadź subskrypcję platformy Azure, której używasz dla hosta. 
      
   d. W polu **Region** (Region) wprowadź region geograficzny, w którym znajduje się Twój host.
      
   d. Na karcie **OS and Size** (System operacyjny i rozmiar) wykonaj następujące czynności:      
      * **Host OS** (System operacyjny hosta): wprowadź system operacyjny maszyny wirtualnej zawierającej hosta. 
      * **Rozmiar**: wprowadź rozmiar maszyny wirtualnej dla hosta.   
       
   e. Na karcie **Resource Group** (Grupa zasobów) wybierz jedną z następujących opcji:      
      * **New resource group** (Nowa grupa zasobów): utwórz grupę zasobów dla swojego hosta.
      * **Existing resource group** (Istniejąca grupa zasobów): podaj istniejącą grupę zasobów z konta platformy Azure. 
       
   f. Na karcie **Network** (Sieć) wybierz jedną z następujących opcji:      
      * **New virtual network** (Nowa sieć wirtualna): utwórz sieć wirtualną dla hosta.
      * **Existing virtual network** (Istniejąca sieć wirtualna): podaj istniejącą sieć wirtualną z konta platformy Azure. 
       
   g. Na karcie **Storage** (Magazyn) wybierz jedną z następujących opcji:      
      * **New storage account** (Nowe konto magazynu): utwórz konto magazynu dla hosta.
      * **Existing storage account** (Istniejące konto magazynu): podaj istniejące konto magazynu z konta platformy Azure.
       
5. Kliknij przycisk **Dalej**.  
     Zostanie otwarte okno **Configure log in credentials and port settings** (Konfiguruj poświadczenia logowania i ustawienia portów).

      ![Okno Configure log in credentials and port settings (Konfiguruj poświadczenia logowania i ustawienia portów)][PUB05]

6. Wybierz jedną z następujących opcji:

   * **Import credentials from Azure Key Vault** (Importuj poświadczenia z usługi Azure Key Vault): określ wcześniej zapisany zestaw poświadczeń, który jest przechowywany w ramach subskrypcji platformy Azure.

       > [!NOTE]
       > Usługa Azure Key Vault, utworzona przy użyciu określonego konta lub jednostki usługi, nie jest automatycznie dostępna w ramach innego konta lub innej jednostki usługi, które współużytkują subskrypcję. Aby umożliwić korzystanie z magazynu kluczy w ramach innego konta lub innej jednostki usługi, należy użyć witryny Azure Portal, aby dodać konto lub jednostkę usługi.

   * **New log in credentials** (Nowe poświadczenia logowania): utwórz nowy zestaw poświadczeń logowania. W przypadku wybrania tej opcji wykonaj następujące czynności:

     a. Na karcie **VM Credentials** (Poświadczenia maszyny wirtualnej) podaj następujące informacje dotyczące poświadczeń logowania maszyny wirtualnej dla hosta platformy Docker:

     * **Nazwa użytkownika**: wprowadź nazwę użytkownika dla poświadczeń logowania do maszyny wirtualnej.

     * **Password** (Hasło) i **Confirm** (Potwierdź): wprowadź hasło dla poświadczeń logowania do maszyny wirtualnej.

     * **SSH**: wprowadź ustawienia Secure Shell (SSH) dla hosta platformy Docker. Możesz wybrać jedną z następujących opcji:

     * **Brak**: określa, że maszyna wirtualna nie zezwala na połączenia SSH.

     * **Auto-generate** (Generuj automatycznie): automatycznie tworzy ustawienia wymagane do nawiązania połączenia za pośrednictwem protokołu SSH.

     * **Import from directory** (Importuj z katalogu): pozwala określić katalog zawierający zestaw poprzednio zapisanych ustawień protokołu SSH. Katalog musi zawierać następujące dwa pliki:

         * *id_rsa*: zawiera identyfikację RSA użytkownika.

         * *id_rsa.pub*: zawiera klucz publiczny RSA służący do uwierzytelniania.

     b. Na karcie **Docker Daemon Access** (Dostęp do demona platformy Docker) podaj następujące informacje:

     ![Tworzenie hosta platformy Docker][PUB06]
    
     * **Docker Daemon port** (Port demona platformy Docker): wprowadź unikatowy port TCP dla hosta platformy Docker.
    
     * **TLS Security** (Zabezpieczenia TLS): wprowadź ustawienia protokołu Transport Layer Security dla hosta platformy Docker. Możesz wybrać z następujących opcji:
    
     * **Brak**: określa, że maszyna wirtualna nie będzie zezwalać na połączenia TLS.
        
     * **Auto-generate** (Generuj automatycznie): automatycznie tworzy ustawienia wymagane do nawiązania połączenia za pośrednictwem protokołu TLS.
        
     * **Import from directory** (Importuj z katalogu): określa katalog zawierający zestaw poprzednio zapisanych ustawień protokołu TLS. Katalog musi zawierać sześć następujących plików:
        
         * *ca.pem* i *ca-key.pem*: zawierają certyfikat i klucz publiczny urzędu certyfikacji TLS.
            
         * *cert.pem* i *key.pem*: zawierają certyfikat klienta i klucz publiczny, które będą używane do uwierzytelniania TLS.
            
         * *server.pem* i *server-key.pem*: zawierają certyfikat klienta i klucz publiczny używane do uwierzytelniania TLS.

7. Po wprowadzeniu wymaganych informacji kliknij przycisk **Finish** (Zakończ).  
    Zostanie ponownie wyświetlony kreator **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure).

   ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB07]

8. Kliknij przycisk **Dalej**.  
    Zostanie otwarte okno **Configure the Docker container to be created** (Konfiguruj kontener platformy Docker do utworzenia).

   ![Okno Configure the Docker container to be created (Konfiguruj kontener platformy Docker do utworzenia)][PUB08]

9. W oknie **Configure the Docker container to be created** (Konfiguruj kontener platformy Docker do utworzenia) podaj następujące informacje: 

   a. W polu **Docker container name** (Nazwa kontenera platformy Docker) wprowadź unikatową nazwę kontenera platformy Docker.

   b. Wybierz jeden z następujących obrazów platformy Docker: 

      * **Predefined Docker image** (Wstępnie zdefiniowany obraz platformy Docker): określ istniejący wcześniej obraz platformy Docker z platformy Azure. 

        > [!NOTE]
        > Lista obrazów platformy Docker w tym polu zawiera kilka obrazów, do których dostosowania skonfigurowano zestaw narzędzi platformy Azure, dzięki czemu artefakt jest wdrażany automatycznie. 

      * **Custom Dockerfile** (Niestandardowy plik Dockerfile): określ wcześniej zapisany plik Dockerfile z komputera lokalnego.

        > [!NOTE]
        > Jest to bardziej zaawansowana funkcja dla deweloperów, którzy chcą wdrożyć własny plik Dockerfile. Deweloperzy, którzy korzystają z tej opcji, muszą jednak zadbać o to, by plik Dockerfile był prawidłowo skompilowany. Ponieważ zestaw narzędzi platformy Azure nie weryfikuje zawartości w niestandardowym pliku Dockerfile, wdrożenie może się nie powieść, jeśli plik Dockerfile zawiera błędy. Ponadto, ze względu na to, że zestaw narzędzi platformy Azure oczekuje, że niestandardowy plik Dockerfile zawiera artefakt aplikacji internetowej, podejmuje próbę nawiązania połączenia protokołu HTTP. Jeśli deweloperzy publikują różne typy artefaktów, po wdrożeniu mogą otrzymywać niegroźne błędy.

   d. W polu **Port settings** (Ustawienia portów) wprowadź unikatowe powiązanie portu TCP dla kontenera platformy Docker. 

10. Po ukończeniu powyższych kroków kliknij przycisk **Finish** (Zakończ). 

Zestaw narzędzi platformy Azure rozpoczyna wdrażanie aplikacji internetowej na platformie Azure w kontenerze platformy Docker. Jeśli środowiska IntelliJ nie skonfigurowano do wdrażania w tle, zostanie wyświetlony pasek postępu **Deploying to Azure** (Wdrażanie na platformie Azure). 

![Pasek postępu wdrażania][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>Dodatkowe informacje na temat tworzenia artefaktów

Aby utworzyć artefakt gotowy do wdrożenia, wykonaj następujące czynności:

1. Otwórz projekt aplikacji internetowej w środowisku IntelliJ.

2. Kliknij menu **File** (Plik), a następnie kliknij pozycję **Project Structure** (Struktura projektu).

   ![Polecenie Project Structure (Struktura projektu)][ART01]

3. Aby dodać artefakt, kliknij zielony znak plus ( **+** ), a następnie pozycję **Web Application: Archive** (Aplikacja internetowa: Archiwum).

   ![Polecenie „Web Application: Archive” (Aplikacja internetowa: Archiwum)][ART02]

4. W polu **Name** (Nazwa) wprowadź nazwę artefaktu (nie dodawaj rozszerzenia  *.war*), a następnie kliknij przycisk **OK**.

   ![Pole nazwy artefaktu][ART03]

Aby uzyskać więcej informacji na temat tworzenia artefaktów w środowisku IntelliJ, zobacz temat [Konfigurowanie artefaktów] (Konfigurowanie artefaktów) w witrynie internetowej JetBrains.

## <a name="next-steps"></a>Następne kroki

Dodatkowe zasoby dotyczące platformy Docker znajdują się w oficjalnej [Witryna internetowa platformy Docker].

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

[Witryna internetowa platformy Docker]: https://www.docker.com/
[Konfigurowanie artefaktów]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[PUB01]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png
