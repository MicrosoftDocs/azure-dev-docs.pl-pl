---
title: Publikowanie aplikacji Spring Boot jako kontenera platformy Docker
titleSuffix: Azure Toolkit for Eclipse
description: Dowiedz się, jak opublikować aplikację internetową na platformie Microsoft Azure jako kontener platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: d11dfce0baa04fd19d54f37832bf61b785818390
ms.sourcegitcommit: fc3408b6e153c847dd90026161c4c498aa06e2fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2019
ms.locfileid: "77000362"
---
# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse-deprecated"></a>Publikowanie aplikacji Spring Boot jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for Eclipse (PRZESTARZAŁE)

[Platforma Spring] jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java.

[Docker] jest rozwiązaniem open-source, które pomaga deweloperom zautomatyzować wdrażanie i skalowanie aplikacji, które działają w kontenerach, oraz zarządzanie nimi.

Ten samouczek zawiera instrukcje wdrażania aplikacji Spring Boot jako kontenera platformy Docker na platformie Microsoft Azure przy użyciu zestawu narzędzi Azure Toolkit for Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repository"></a>Klonowanie domyślnego repozytorium Spring Boot Docker

### <a name="import-the-public-repository"></a>Importowanie repozytorium publicznego

Poniższe kroki zawierają instrukcje klonowania repozytorium Spring Boot Docker na komputer lokalny przy użyciu środowiska IntelliJ. Jeśli chcesz użyć wiersza polecenia, zobacz temat [Deploy a Spring Boot application on Linux in Azure Container Service][Deploy Spring Boot on Linux in AKS] (Wdrażanie aplikacji Spring Boot w systemie Linux w usłudze Azure Container Service).

1. Otwórz program Eclipse.

1. Kliknij kolejno pozycje **File (Plik)**  > **Import (Importuj)** .

   ![Importowanie za pomocą menu Plik][CL01]

1. Po otwarciu okna dialogowego **Import** (Importowanie):

   a. Rozwiń węzeł **Git**.

   b. Wybierz pozycję **Projects from Git** (Projekty usługi Git).
   
   d. Kliknij przycisk **Dalej**.

   ![Okno dialogowe Import (Importowanie)][CL02]

1. Na stronie **Select Repository Source** (Wybieranie źródła repozytorium):

   a. Wybierz pozycję **Clone URI** (Sklonuj identyfikator URI).
   
   b. Kliknij przycisk **Dalej**.

   ![Strona Select Repository Source (Wybieranie źródła repozytorium)][CL03]

1. Na stronie **Source Git Repository** (Źródłowe repozytorium Git):

   a. W polu **URI** wprowadź `https://github.com/spring-guides/gs-spring-boot-docker.git`. Ta czynność powinna spowodować automatyczne wypełnienie pól **Host** (Host) i **Repository path** (Ścieżka repozytorium) prawidłowymi wartościami.
   
   b. Repozytorium Spring Boot jest publiczne, więc nie powinno być konieczności wprowadzenia nazwy użytkownika i hasła usługi Git.
   
   d. Kliknij przycisk **Dalej**.

   ![Strona Source Git Repository (Źródłowe repozytorium Git)][CL04]

1. Na stronie **Branch Selection** (Wybór gałęzi) kliknij przycisk **Next** (Dalej).

   ![Strona Branch Selection (Wybór gałęzi)][CL05]

1. Na stronie **Local Destination** (Lokalny obiekt docelowy):

   a. Określ folder lokalny, w którym chcesz utworzyć repozytorium lokalne.
   
   b. Kliknij przycisk **Dalej**.

   ![Strona Local Destination (Lokalny obiekt docelowy)][CL06]

1. Na stronie **Select a wizard to use for importing projects** (Wybierz kreatora, który zostanie użyty do zaimportowania projektów):

   a. Wybierz pozycję **Import as a general project** (Zaimportuj jako projekt ogólny).
   
   b. Kliknij przycisk **Dalej**.

   ![Strona „Select a wizard to use for importing projects” (Wybierz kreatora, który zostanie użyty do zaimportowania projektów)][CL07]

1. Na stronie **Import Projects** (Importowanie projektów):

   a. Podaj nazwę projektu.
   
   b. Kliknij przycisk **Zakończ**.

   ![Strona Import Projects (Importowanie projektów)][CL08]

1. Po pomyślnym sklonowaniu repozytorium wszystkie pliki będą widoczne w środowisku Eclipse.

   ![Lokalne repozytorium][CL09]

### <a name="create-a-maven-project-from-your-local-repository"></a>Tworzenie projektu Maven z lokalnego repozytorium

Repozytorium Spring Boot Docker zawiera kompletny projekt Maven, który zostanie użyty w tym samouczku. 

1. Kliknij kolejno pozycje **File (Plik)**  > **Import (Importuj)** .

   ![Polecenie Import (Importuj) w menu File (Plik)][CL01]

1. Po otwarciu okna dialogowego **Import** (Importowanie):

   a. Rozwiń węzeł **Maven**.
   
   b. Wybierz pozycję **Existing Maven Projects** (Istniejące projekty Maven).
   
   d. Kliknij przycisk **Dalej**.

   ![Okno dialogowe Import (Importowanie)][MV01]

1. Na stronie **Maven Projects** (Projekty Maven):

   a. W polu **Root Directory** (Katalog główny) podaj folder **complete** z repozytorium lokalnego.
   
   b. Rozwiń sekcję **Advanced** (Zaawansowane) i wprowadź nazwę niestandardową w polu **Name template** (Szablon nazwy).
   
   d. W sekcji Projects (Projekty) zaznacz pole obok pliku **pom.xml**.
   
   d. Kliknij przycisk **Zakończ**.

   ![Strona Maven Projects (Projekty Maven)][MV02]

1. Po pomyślnym otwarciu projektu Maven w środowisku Eclipse jest widoczny drugi projekt.

   ![Lokalny projekt Maven][MV03]

## <a name="build-your-spring-boot-app-by-using-maven"></a>Tworzenie aplikacji Spring Boot przy użyciu programu Maven

1. W widoku Project Explorer (Eksplorator projektu) środowiska Eclipse wybierz projekt Maven.

1. Kliknij kolejno pozycje **Run (Uruchom)**  > **Run As (Uruchom jako)**  > **Maven build (Kompilacja Maven)** .

   ![Polecenia do uruchomienia jako kompilacja Maven][BU01]

1. Po pomyślnym skompilowaniu aplikacji w oknie konsoli jest wyświetlany stan.

   ![Pomyślna kompilacja Maven][BU02]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Publikowanie aplikacji internetowej na platformie Azure przy użyciu kontenera platformy Docker

1. W widoku Project Explorer (Eksplorator projektu) środowiska Eclipse wybierz projekt Maven.

1. Kliknij menu **Publish** (Publikuj) platformy Azure, a następnie kliknij pozycję **Publish as Docker Container** (Publikuj jako kontener platformy Docker).

   ![Polecenie Publish as Docker Container (Publikuj jako kontener platformy Docker)][PU01]

1. Po wyświetleniu okna dialogowego **Deploying Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure):

   a. Wprowadź niestandardową nazwę obrazu platformy Docker.
   
   b. W polu **Artifact to deploy** (Artefakt do wdrożenia) podaj ścieżkę do utworzonego przez siebie pliku **gs-spring-boot-docker-0.1.0.jar**.

   ![Określenie opcji platformy Docker][PU02]

   Zostaną wyświetlone wszystkie istniejące hosty platformy Docker. 

1. Jeśli zdecydujesz się na wdrożenie na istniejącym hoście, możesz od razu przejść do kroku 5. W przeciwnym razie wykonaj następujące czynności, aby utworzyć hosta:

   a. Kliknij pozycję **Add** (Dodaj).

      ![Dodawanie nowego hosta platformy Docker][PU03]

   b. Po wyświetleniu okna dialogowego **Create Docker Host** (Tworzenie hosta platformy Docker) możesz zaakceptować ustawienia domyślne lub podać ustawienia niestandardowe dla nowego hosta platformy Docker. (Aby zapoznać się ze szczegółowymi opisami różnych ustawień, zobacz temat [Publikowanie aplikacji internetowej jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ][Publish Container with Azure Toolkit]). Po określeniu odpowiednich ustawień kliknij przycisk **Next** (Dalej).

      ![Określanie opcji hosta platformy Docker][PU04]

   d. Możesz wybrać opcję używania istniejących poświadczeń logowania z usługi Azure Key Vault lub wprowadzić nowe poświadczenia logowania platformy Docker. Po określeniu opcji kliknij przycisk **Finish** (Zakończ).

      ![Określanie poświadczeń hosta platformy Docker][PU05]

1. Wybierz odpowiedniego hosta platformy Docker, a następnie kliknij przycisk **Next** (Dalej).

   ![Wybranie hosta platformy Docker do użycia][PU06]

1. Na ostatniej stronie okna dialogowego **Deploying Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure) określ następujące opcje:

   a. Możesz nadać kontenerowi, który będzie hostować kontener platformy Docker, nazwę niestandardową lub zaakceptować nazwę domyślną.

   b. Wprowadź porty TCP dla hosta platformy Docker, używając następującej składni: *[port zewnętrzny]* : *[port wewnętrzny]* . Na przykład **80:8080** oznacza port zewnętrzny 80 oraz domyślny port wewnętrzny Spring Boot 8080.
   
      Jeśli dostosowano port wewnętrzny (na przykład przez edytowanie pliku application.yml), należy podać numer portu, co zapewni prawidłowe wyznaczanie tras na platformie Azure.

   d. Po skonfigurowaniu tych opcji kliknij przycisk **Finish** (Zakończ).

   ![Wdrażanie kontenera platformy Docker na platformie Azure][PU07]

1. Po zakończeniu publikowania przez zestaw narzędzi platformy Azure w obszarze Azure Activity Log (Dziennik aktywności platformy Azure) będzie wyświetlany stan **Published** (Opublikowano).

   ![Pomyślnie wdrożony host platformy Docker][PU08]

## <a name="next-steps"></a>Następne kroki

Dodatkowe zasoby dotyczące platformy Docker znajdują się w oficjalnej [witrynie internetowej platformy Docker](https://www.docker.com/).

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: https://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in AKS]: /azure/container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: azure-toolkit-for-eclipse-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[CL01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png
