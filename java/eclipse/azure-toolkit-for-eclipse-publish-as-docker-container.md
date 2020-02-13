---
title: Publikowanie aplikacji internetowej jako kontenera platformy Docker
titleSuffix: Azure Toolkit for Eclipse
description: Dowiedz się, jak opublikować aplikację internetową na platformie Microsoft Azure jako kontener platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: d1e384525ec32141b945f50d1d5be7190e84b239
ms.sourcegitcommit: fc3408b6e153c847dd90026161c4c498aa06e2fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2019
ms.locfileid: "77000376"
---
# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse-deprecated"></a>Publikowanie aplikacji internetowej jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for Eclipse (PRZESTARZAŁE)

Użycie kontenerów platformy Docker jest powszechnie stosowaną metodą wdrażania aplikacji internetowych. Przy użyciu kontenerów platformy Docker deweloperzy mogą skonsolidować wszystkie pliki projektu i zależności w jednym pakiecie w celu wdrożenia na serwerze. Zestaw narzędzi Azure Toolkit for Eclipse pozwala uprościć ten proces dla deweloperów języka Java dzięki dodaniu funkcji *Publish as Docker Container* (Publikowanie jako kontener platformy Docker) na potrzeby wdrażania na platformie Microsoft Azure. Ten artykuł zawiera instrukcje publikowania aplikacji na platformie Azure jako kontenerów platformy Docker.

> [!NOTE]
> Więcej informacji na temat platformy Docker znajduje się w [Witryna internetowa platformy Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Publikowanie aplikacji internetowej na platformie Azure przy użyciu kontenera platformy Docker

1. Otwórz projekt aplikacji internetowej w środowisku Eclipse.

2. Aby uruchomić kreatora **Publish as Docker Container** (Publikowanie jako kontener platformy Docker), wykonaj jedną z następujących czynności:

   * W widoku **Nawigator** kliknij prawym przyciskiem myszy projekt, kliknij pozycję **Azure**, a następnie kliknij pozycję **Publish as Docker Container** (Publikuj jako kontener platformy Docker).

      ![Polecenie w widoku Nawigatora Publish as Docker Container (Publikuj jako kontener platformy Docker)][PUB01]

   * Na pasku narzędzi w środowisku Eclipse kliknij przycisk **Publish** (Publikuj), a następnie kliknij pozycję **Publish as Docker Container** (Publikuj jako kontener platformy Docker).

      ![Polecenie na pasku narzędzi w środowisku Eclipse Publikuj jako kontener platformy Docker][PUB02]
      
   Zostanie otwarty kreator **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure).

   ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB03]

3. W oknie **Type an image name, select the artifact's path and check a Docker host to be used** (Wpisz nazwę obrazu, wybierz ścieżkę artefaktu i sprawdź hosta platformy Docker, który zostanie użyty), wykonaj następujące czynności:

   a. W polu **Docker image name** (Nazwa obrazu platformy Docker) wprowadź unikatową nazwę hosta platformy Docker. (Kreator automatycznie tworzy nazwę, ale można ją zmodyfikować).

   b. W obszarze **Hosts** (Hosty) zostaną wyświetlone wszystkie hosty platformy Docker, które zostały już utworzone. Wykonaj jedną z następujących czynności:

   * Jeśli masz istniejącego hosta platformy Docker, możesz wdrożyć na nim swoją aplikację internetową.
   * Aby utworzyć nowego hosta platformy Docker, kliknij przycisk **Dodaj**.  
      
      Zostanie otwarte okno dialogowe **Create Docker Host** (Tworzenie hosta platformy Docker).

   ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB04a]

4. W oknie **Configure the new virtual machine** (Konfigurowanie nowej maszyny wirtualnej) określ następujące opcje dla hosta platformy Docker. (Kreator automatycznie generuje większość wartości opcji, ale można je modyfikować).

   a. **Nazwa**: Wprowadź unikatową nazwę hosta platformy Docker. (Nie jest taka sama jak określona wcześniej nazwa obrazu platformy Docker).

   b. **Subskrypcja**: Wprowadź subskrypcję platformy Azure, której używasz dla hosta.

   d. **Region**: Wprowadź region geograficzny, w którym znajduje się Twój host.

   d. Na karcie **Host OS and Size** (System operacyjny i rozmiar hosta): 
   * **Host OS** (System operacyjny hosta): Wprowadź system operacyjny maszyny wirtualnej zawierającej hosta.
   * **Rozmiar**: Wprowadź rozmiar maszyny wirtualnej dla hosta.

   e. Na karcie **Resource Group** (Grupa zasobów): 
   * **New resource group** (Nowa grupa zasobów): Utwórz nową grupę zasobów dla hosta.
   * **Existing resource group** (Istniejąca grupa zasobów): Wprowadź istniejącą grupę zasobów z konta platformy Azure.

   f. Na karcie **Network** (Sieć): 
   * **New virtual network** (Nowa sieć wirtualna): Utwórz nową sieć wirtualną dla hosta.
   * **Existing virtual network** (Istniejąca sieć wirtualna): Wprowadź istniejącą sieć wirtualną z konta platformy Azure.

   g. Na karcie **Storage** (Magazyn): 
   * **New storage account** (Nowe konto magazynu): Utwórz nowe konto magazynu dla hosta.
   * **Existing storage account** (Istniejące konto magazynu): Wprowadź istniejące konto magazynu z konta platformy Azure.

5. Kliknij przycisk **Dalej**.

6. W oknie **Configure log in credentials and port settings** (Konfiguruj poświadczenia logowania i ustawienia portu) wybierz jedną z następujących opcji:

   * **Import credentials from Azure Key Vault** (Importuj poświadczenia z usługi Azure Key Vault): Określa wcześniej zapisany zestaw poświadczeń, które są przechowywane w ramach subskrypcji platformy Azure. 

   >[!NOTE]
   >Usługa Azure Key Vault, utworzona przy użyciu określonego konta lub jednostki usługi, nie jest automatycznie dostępna w ramach innego konta lub innej jednostki usługi, które współużytkują subskrypcję. Aby umożliwić korzystanie z usługi Key Vault w ramach innego konta lub innej jednostki usługi, należy użyć witryny Azure Portal, aby dodać konto lub jednostkę usługi.
   >

   * **New log in credentials** (Nowe poświadczenia logowania): Tworzy nowy zestaw poświadczeń logowania. W przypadku wybrania tej opcji wykonaj następujące czynności: 
    
     * Na karcie **VM Credentials** (Poświadczenia maszyny wirtualnej) wybierz jedną z następujących opcji poświadczeń logowania maszyny wirtualnej dla hosta platformy Docker: 

       * **Nazwa użytkownika**: Wprowadź nazwę użytkownika dla poświadczeń logowania do maszyny wirtualnej. 
       * **Password** (Hasło) i **Confirm** (Potwierdź): Wprowadź hasło dla poświadczeń logowania do maszyny wirtualnej. 
       * **SSH**: Wprowadź ustawienia Secure Shell (SSH) dla hosta platformy Docker. Możesz wybrać z następujących opcji: 
          * **Brak**: Określa, że maszyna wirtualna nie będzie zezwalać na połączenia SSH. 
          * **Auto-generate** (Generuj automatycznie): Automatycznie tworzy ustawienia wymagane do nawiązania połączenia za pośrednictwem protokołu SSH. 
          * **Import from directory** (Importuj z katalogu): Określa katalog zawierający zestaw poprzednio zapisanych ustawień protokołu SSH. Katalog musi zawierać następujące dwa pliki: 
             * *id_rsa*: zawiera identyfikację RSA użytkownika. 
             * *id_rsa.pub*: zawiera klucz publiczny RSA służący do uwierzytelniania. 
        
         ![Tworzenie hosta platformy Docker][PUB05]

     * Na karcie **Docker Daemon Credentials** (Poświadczenia demona platformy Docker) określ następujące opcje: 

       * **Docker Daemon port** (Port demona platformy Docker): Wprowadź unikatowy port TCP dla hosta platformy Docker. 
       * **TLS Security** (Zabezpieczenia TLS): Wprowadź ustawienia protokołu Transport Layer Security dla hosta platformy Docker. Możesz wybrać z następujących opcji: 
          * **Brak**: Określa, że maszyna wirtualna nie będzie zezwalać na połączenia TLS. 
          * **Auto-generate** (Generuj automatycznie): Automatycznie tworzy ustawienia wymagane do nawiązania połączenia za pośrednictwem protokołu TLS. 
          * **Import from directory** (Importuj z katalogu): Określa katalog zawierający zestaw poprzednio zapisanych ustawień protokołu TLS. Dokładniej, katalog musi zawierać sześć następujących plików: 
             * *ca.pem* i *ca-key.pem*: zawierają certyfikat i klucz publiczny urzędu certyfikacji TLS. 
             * *cert.pem* i *key.pem*: zawierają certyfikat klienta i klucz publiczny używane do uwierzytelniania TLS. 
             * *server.pem* i *server-key.pem*: zawierają certyfikat serwera i klucz publiczny dla hosta. 

         ![Tworzenie hosta platformy Docker][PUB06]

7. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Finish** (Zakończ).

8. W kreatorze **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure) kliknij przycisk **Next** (Dalej).

   ![Kreator Deploy Docker Container on Azure (Wdrażanie kontenera platformy Docker na platformie Azure)][PUB07]

9. W oknie **Configure the Docker container to be created** (Konfiguruj kontener platformy Docker do utworzenia) wykonaj następujące czynności:

   a. W polu **Docker container name** (Nazwa kontenera platformy Docker) wprowadź unikatową nazwę kontenera platformy Docker.

   b. Wybierz jeden z następujących obrazów platformy Docker: 

   * **Predefined Docker image** (Wstępnie zdefiniowany obraz platformy Docker): Określa istniejący wcześniej obraz platformy Docker z platformy Azure. 

     >[!NOTE]
     >Lista obrazów platformy Docker w tym polu zawiera kilka obrazów, do których dostosowania skonfigurowano zestaw narzędzi platformy Azure, dzięki czemu artefakt jest wdrażany automatycznie.
     >

   * **Custom Dockerfile** (Niestandardowy plik Dockerfile): Określa wcześniej zapisany plik Dockerfile z komputera lokalnego.

     >[!NOTE]
     >Jest to bardziej zaawansowana funkcja dla deweloperów, którzy chcą wdrożyć własny plik Dockerfile. Deweloperzy, którzy korzystają z tej opcji, muszą jednak zadbać o to, by plik Dockerfile był prawidłowo skompilowany. Zestaw narzędzi platformy Azure nie weryfikuje zawartości w niestandardowym pliku Dockerfile, dlatego wdrożenie może się nie powieść, jeśli plik Dockerfile zawiera błędy. Ponadto zestaw narzędzi platformy Azure oczekuje, że niestandardowy plik Dockerfile zawiera artefakt aplikacji internetowej, i podejmie próbę nawiązania połączenia protokołu HTTP. Jeśli deweloperzy publikują różne typy artefaktów, po wdrożeniu mogą otrzymywać niegroźne błędy.
     >

   d. **Port settings** (Ustawienia portu): Wprowadź unikatowe powiązanie portu TCP dla kontenera platformy Docker.

      ![Okno Configure the Docker container to be created (Konfiguruj kontener platformy Docker do utworzenia)][PUB08]

10. Po ukończeniu wszystkich powyższych kroków kliknij przycisk **Finish** (Zakończ).

Zestaw narzędzi platformy Azure rozpoczyna wdrażanie aplikacji internetowej na platformie Azure w kontenerze platformy Docker. 

## <a name="next-steps"></a>Następne kroki

Dodatkowe zasoby dotyczące platformy Docker znajdują się w oficjalnej [Witryna internetowa platformy Docker].

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Witryna internetowa platformy Docker]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png
