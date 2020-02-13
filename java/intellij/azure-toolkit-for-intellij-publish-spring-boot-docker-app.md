---
title: Publikowanie aplikacji Spring Boot jako kontenera platformy Docker
titleSuffix: Azure Toolkit for IntelliJ
description: Dowiedz się, jak opublikować aplikację internetową na platformie Microsoft Azure jako kontener platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ.
documentationcenter: java
ms.date: 11/13/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 00d6d77c78a1b38bbeb5369301956b2cfce166a5
ms.sourcegitcommit: fc3408b6e153c847dd90026161c4c498aa06e2fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2019
ms.locfileid: "77000369"
---
# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij-deprecated"></a>Publikowanie aplikacji Spring Boot jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ (PRZESTARZAŁE)

[Platforma Spring] jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java.

[Docker] jest rozwiązaniem open-source, które pomaga deweloperom zautomatyzować wdrażanie i skalowanie aplikacji, które działają w kontenerach, oraz zarządzanie nimi.

Ten samouczek zawiera instrukcje wdrażania aplikacji Spring Boot jako kontenera platformy Docker na platformie Microsoft Azure przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repo"></a>Klonowanie domyślnego repozytorium Spring Boot Docker

Poniższe kroki zawierają instrukcje klonowania repozytorium Spring Boot Docker przy użyciu środowiska IntelliJ. Jeśli chcesz użyć wiersza polecenia, zobacz temat [Deploy a Spring Boot application on Linux in Azure Container Service][Deploy Spring Boot on Linux in AKS] (Wdrażanie aplikacji Spring Boot w systemie Linux w usłudze Azure Container Service).

1. Otwórz środowisko IntelliJ.

1. Na ekranie powitalnym wybierz pozycję **GitHub** na liście **Check out from Version Control** (Wyewidencjonuj z kontroli wersji).

   ![Opcja GitHub na potrzeby kontroli wersji][CL01]

1. Jeśli zostanie wyświetlony monit o zalogowanie się, wprowadź swoje poświadczenia.

   * Jeśli do zalogowania się do serwisu GitHub używasz nazwy użytkownika/hasła: 

      ![Okno dialogowe do wprowadzenia nazwy użytkownika i hasła w serwisie GitHub][CL02a]

   * Jeśli do zalogowania się do serwisu GitHub używasz tokenu: 

      ![Okno dialogowe do wprowadzenia tokenu w serwisie GitHub][CL02b]

1. Jako adres URL repozytorium wprowadź **https://github.com/spring-guides/gs-spring-boot-docker.git** , podaj ścieżkę lokalną i informacje dotyczące folderu, a następnie kliknij przycisk **Clone** (Sklonuj).

   ![Okno dialogowe klonowania repozytorium][CL03]

1. Po wyświetleniu monitu o utworzenie projektu IntelliJ wybierz przycisk **No** (Nie).

   ![Odrzucanie tworzenia projektu IntelliJ][CL04]

1. Na stronie powitalnej kliknij pozycję **Import Project** (Zaimportuj projekt).

   ![Wybór pozycji Import Project (Zaimportuj projekt)][CL05]

1. Znajdź ścieżkę, do której zostało sklonowane repozytorium Spring Boot, wybierz folder **complete** w katalogu głównym, a następnie kliknij przycisk **OK**.

   ![Wybieranie folderu do zaimportowania][CL06]

1. Po wyświetleniu monitu wybierz pozycję **Create project from existing sources** (Utwórz projekt z istniejących źródeł).

   ![Opcja tworzenia projektu z istniejących źródeł][CL07]

1. Podaj nazwę projektu lub zaakceptuj domyślną, sprawdź poprawność ścieżki do folderu **complete**, a następnie kliknij przycisk **Next** (Dalej).

   ![Podanie nazwy projektu][CL08]

1. Dostosuj wszystkie katalogi do zaimportowania, a następnie kliknij przycisk **Next** (Dalej).

   ![Wybieranie katalogów][CL09]

1. Przejrzyj biblioteki do zaimportowania, a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądanie bibliotek projektu][CL10]

1. Przejrzyj strukturę modułu, a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądanie struktury modułu][CL11]

1. Podaj zestaw JDK, a następnie kliknij przycisk **Next** (Dalej).

   ![Podanie zestawu JDK][CL12]

1. Kliknij przycisk **Zakończ**.

   ![Przycisk Finish (Zakończ)][CL13]

Platforma IntelliJ importuje aplikację Spring Boot jako projekt i wyświetla strukturę po zakończeniu importu.

![Aplikacja Spring Boot w środowisku IntelliJ][CL14]

## <a name="build-your-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

### <a name="build-the-app-by-using-the-maven-pom"></a>Tworzenie aplikacji za pomocą modelu POM narzędzia Maven

1. Otwórz okno narzędzia Maven, jeśli nie zostało jeszcze otwarte. Kliknij kolejno pozycje **View (Widok)**  > **Tool Windows (Okna narzędzi)**  > **Maven Projects (Projekty Maven)** .

   ![Polecenia do opcji Okna narzędzi i Projekty Maven][BU01]

1. W oknie narzędzia Maven kliknij prawym przyciskiem myszy pozycję **package** i wybierz pozycję **Run Maven Build** (Uruchom kompilację Maven). (Jeśli projekt Maven nie zostanie automatycznie wyświetlony, kliknij ikonę **Reimport** (Ponownie zaimportuj) na pasku narzędzi Maven).

   ![Polecenie Run Maven Build (Uruchom kompilację Maven)][BU02]

1. Po pomyślnym utworzeniu aplikacji Spring Boot platforma IntelliJ powinna wyświetlić komunikat **BUILD SUCCESS** (POMYŚLNE UTWORZENIE).

   ![Komunikat BUILD SUCCESS (POMYŚLNE UTWORZENIE)][BU03]

### <a name="create-a-deployment-ready-artifact"></a>Tworzenie artefaktu gotowego do wdrożenia

Aby opublikować aplikację Spring Boot, należy utworzyć artefakt gotowy do wdrożenia. Wykonaj następujące czynności:

1. Otwórz projekt aplikacji internetowej w środowisku IntelliJ.

1. Kliknij menu **File** (Plik), a następnie kliknij pozycję **Project Structure** (Struktura projektu).

   ![Polecenie Project Structure (Struktura projektu)][ART01]

1. Kliknij zielony znak plus ( **+** ), aby dodać artefakt, kliknij pozycję **JAR**, a następnie kliknij pozycję **Empty** (Puste).

   ![Dodawanie artefaktu][ART02]

1. Nadaj artefaktowi nazwę, pamiętając, aby nie dodać rozszerzenia „.jar”, a następnie podaj folder docelowy dla danych wyjściowych narzędzia Maven.

   ![Określenie właściwości artefaktu][ART03]

1. Utwórz dla artefaktu manifest (opcjonalnie):

   a. Kliknij przycisk **Create Manifest** (Utwórz manifest).

      ![Kliknięcie przycisku Create Manifest (Utwórz manifest)][ART04a]

   b. Wybierz ścieżkę domyślną artefaktu, a następnie kliknij przycisk **OK**.

      ![Określenie ścieżki artefaktu][ART04b]

   d. Kliknij wielokropek ( **...** ), aby znaleźć klasę główną.

      ![Znajdowanie klasy głównej][ART04c]

   d. Wybierz klasę główną, a następnie kliknij przycisk **OK**.

      ![Określenie klasy głównej][ART04d]

1. Kliknij przycisk **OK**.

   ![Zamknięcie okna dialogowego Project Structure (Struktura projektu)][ART05]

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia artefaktów w środowisku IntelliJ, zobacz temat [Konfigurowanie artefaktów] (Konfigurowanie artefaktów) w witrynie internetowej JetBrains.
>

### <a name="build-the-artifact-for-deployment"></a>Tworzenie artefaktu do wdrożenia

1. Kliknij menu **Build** (Utwórz), a następnie kliknij pozycję **Build Artifacts** (Utwórz artefakty).

   ![Polecenie Build Artifacts (Utwórz artefakty)][BU04]

1. Po wyświetlenie menu kontekstowego **Build Artifact** (Utwórz artefakt) kliknij pozycję **Build** (Utwórz).

   ![Menu kontekstowe Build Artifact (Utwórz artefakt)][BU05]

Na platformie IntelliJ kompletne artefakty aplikacji Spring Boot powinny zostać wyświetlone w oknie narzędzi projektu.

   ![Utworzony artefakt][BU06]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Publikowanie aplikacji internetowej na platformie Azure przy użyciu kontenera platformy Docker

1. Jeśli wcześniej nie nastąpiło logowanie do platformy Azure, wykonaj czynności opisane w temacie [Sign-in instructions for the Azure Toolkit for IntelliJ][Azure Sign In for IntelliJ] (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for IntelliJ).

1. W oknie narzędzi w widoku Project Explorer (Eksplorator projektów) kliknij prawym przyciskiem myszy projekt, a następnie wybierz opcje **Azure** > **Publish as Docker Container** (Publikuj jako kontener platformy Docker).

   ![Polecenie Publish as Docker Container (Publikuj jako kontener platformy Docker)][PU01]

1. Po wyświetleniu okna dialogowego **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure), zostaną wyświetlone wszystkie istniejące hosty platformy Docker. Jeśli zdecydujesz się na wdrożenie na istniejącym hoście, możesz od razu przejść do kroku 4. W przeciwnym razie wykonaj następujące czynności, aby utworzyć hosta:

   a. Kliknij zielony znak plus ( **+** ).

      ![Dodawanie nowego hosta platformy Docker][PU02]

   b. Po wyświetleniu okna dialogowego **Create Docker Host** (Tworzenie hosta platformy Docker) możesz zaakceptować ustawienia domyślne lub podać ustawienia niestandardowe dla nowego hosta platformy Docker. (Aby zapoznać się ze szczegółowymi opisami różnych ustawień, zobacz temat [Publikowanie aplikacji internetowej jako kontenera platformy Docker przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ][Publish Container with Azure Toolkit]). Po określeniu odpowiednich ustawień kliknij przycisk **Next** (Dalej).

      ![Określanie opcji hosta platformy Docker][PU03a]

   d. Możesz wybrać opcję używania istniejących poświadczeń logowania z usługi Azure Key Vault lub wprowadzić nowe poświadczenia logowania platformy Docker. Po określeniu opcji kliknij przycisk **Finish** (Zakończ).

      ![Określanie poświadczeń hosta platformy Docker][PU03b]

1. Wybierz odpowiedniego hosta platformy Docker, a następnie kliknij przycisk **Next** (Dalej).

   ![Wybranie hosta platformy Docker do użycia][PU04]

1. Na ostatniej stronie okna dialogowego **Deploy Docker Container on Azure** (Wdrażanie kontenera platformy Docker na platformie Azure) określ następujące opcje:

   a. Możesz nadać kontenerowi, który będzie hostować kontener platformy Docker, nazwę niestandardową lub zaakceptować nazwę domyślną.

   b. Wprowadź porty TCP dla hosta platformy Docker, używając następującej składni: *[port zewnętrzny]* : *[port wewnętrzny]* . Na przykład **80:8080** oznacza port zewnętrzny 80 oraz domyślny port wewnętrzny Spring Boot 8080.
   
      Jeśli dostosowano port wewnętrzny (na przykład przez edytowanie pliku application.yml), należy podać numer portu, co zapewni prawidłowe wyznaczanie tras na platformie Azure.

   d. Po skonfigurowaniu tych opcji kliknij przycisk **Finish** (Zakończ).

   ![Wdrażanie kontenera platformy Docker na platformie Azure][PU05]

1. Po zakończeniu publikowania przez zestaw narzędzi platformy Azure w obszarze Azure Activity Log (Dziennik aktywności platformy Azure) będzie wyświetlany stan **Published** (Opublikowano).

   ![Pomyślnie wdrożony host platformy Docker][PU06]

## <a name="next-steps"></a>Następne kroki

Aby poznać inne metody tworzenia aplikacji Spring Boot w środowisku IntelliJ, zobacz temat [Creating Spring Boot Projects](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html) (Tworzenie projektów Spring Boot) w witrynie internetowej JetBrains.

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: https://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Konfigurowanie artefaktów]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html
[Deploy Spring Boot on Linux in AKS]: /azure/container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[CL01]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png
