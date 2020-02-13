---
title: Wdrażanie aplikacji internetowej Hello world w kontenerze systemu Linux
titleSuffix: Azure Toolkit for IntelliJ
description: Uruchom podstawową aplikację internetową Hello world w kontenerze systemu Linux i wdróż ją w chmurze przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ.
services: app-service\web
documentationcenter: java
ms.date: 12/20/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 3e36123086486e8f1d98ca135d4360962d0d23a8
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999494"
---
# <a name="deploy-a-hello-world-web-app-to-a-linux-container-in-the-cloud-using-the-azure-toolkit-for-intellij"></a>Wdrażanie aplikacji internetowej Hello world w kontenerze systemu Linux w chmurze przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ

Użycie kontenerów platformy [Docker] jest powszechnie stosowaną metodą wdrażania aplikacji internetowych. Przy użyciu kontenerów platformy Docker deweloperzy mogą skonsolidować wszystkie pliki projektu i zależności w jednym pakiecie w celu wdrożenia na serwerze. Zestaw narzędzi Azure Toolkit for IntelliJ pozwala uprościć ten proces dla deweloperów języka Java dzięki dodaniu funkcji na potrzeby wdrażania na platformie Microsoft Azure.

W tym artykule przedstawiono kroki wymagane do utworzenia podstawowej aplikacji internetowej Hello world i opublikowania aplikacji internetowej w kontenerze systemu Linux na platformie Azure przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]
* Klient platformy [Docker].

> [!NOTE]
>
> Aby wykonać kroki opisane w tym samouczku, należy skonfigurować platformę [Docker] do uwidaczniania demona na porcie 2375 bez protokołu TLS. To ustawienie można skonfigurować podczas instalowania platformy Docker lub za pomocą menu ustawień platformy Docker.
>
> ![Menu ustawień platformy Docker][docker-settings-menu]
>

## <a name="create-a-new-web-app-project"></a>Tworzenie projektu nowej aplikacji internetowej

1. Uruchom platformę IntelliJ i zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w artykule [Sign In Instructions for the Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-sign-in-instructions) (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for IntelliJ).

1. Kliknij menu **File** (Plik), a następnie pozycje **New** (Nowy) i **Project** (Projekt).
   
   ![Tworzenie nowego projektu][file-new-project]

1. W oknie dialogowym **New Project** (Nowy projekt) wybierz kolejno pozycje **Maven** i **maven-archetype-webapp**, a następnie kliknij pozycję **Next** (Dalej).
   
   ![Wybieranie aplikacji internetowej archetypu programu Maven][maven-archetype-webapp]
   
1. Określ wartości **GroupId** (Identyfikator grupy) i **ArtifactId** (Identyfikator artefaktu) dla aplikacji internetowej, a następnie kliknij przycisk **Next** (Dalej).
   
   ![Określanie wartości GroupId (Identyfikator grupy) i ArtifactId (Identyfikator artefaktu)][groupid-and-artifactid]

1. Dostosuj ustawienia programu Maven lub zaakceptuj wartości domyślne, a następnie kliknij przycisk **Next** (Dalej).
   
   ![Określanie ustawień programu Maven][maven-options]

1. Określ nazwę i lokalizację projektu, a następnie kliknij pozycję **Finish** (Zakończ).
   
   ![Określanie nazwy projektu][project-name]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Tworzenie rejestru kontenerów platformy Azure do użytku jako prywatny rejestr platformy Docker

W poniższych krokach objaśniono sposób tworzenia rejestru kontenerów platformy Azure przy użyciu witryny Azure Portal.

> [!NOTE]
>
> Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure zamiast witryny Azure Portal, wykonaj kroki opisane w artykule [Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0][Create Docker Registry using Azure CLI].
>

1. Przejdź do witryny [Azure Portal] i zaloguj się.

   Po zalogowaniu się do konta w witrynie Azure Portal wykonaj kroki opisane w artykule [Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą witryny Azure Portal], które sparafrazowano poniżej ze względów praktycznych.

1. Kliknij ikonę menu **+ Utwórz zasób**, kliknij pozycję **Kontenery**, a następnie kliknij pozycję **Rejestr kontenerów**.
   
   ![Tworzenie nowego rejestru kontenerów platformy Azure][create-container-registry-01]

1. Na wyświetlonej stronie **Tworzenie rejestru kontenerów** wprowadź **nazwę rejestru** i **grupę zasobów**, wybierz pozycję **Włącz** dla **administratora**, a następnie kliknij pozycję **Utwórz**.

   ![Konfigurowanie ustawień rejestru kontenerów platformy Azure][create-container-registry-02]

## <a name="deploy-your-web-app-in-a-docker-container"></a>Wdrażanie aplikacji internetowej w kontenerze platformy Docker

1. Kliknij prawym przyciskiem myszy projekt w eksploratorze projektu, wybierz pozycję **Azure**, a następnie kliknij pozycję **Add Docker Support** (Dodaj obsługę platformę Docker).

   Spowoduje to automatyczne utworzenie pliku platformy Docker z konfiguracją domyślną.

   ![Dodawanie obsługi platformy Docker][add-docker-support]

1. Po dodaniu obsługi platformy Docker kliknij prawym przyciskiem myszy projekt w eksploratorze projektu, wybierz pozycję **Azure**, a następnie kliknij polecenie **Run on Web App for Containers** (Uruchom w obrębie funkcji Web App for Containers).

   ![Uruchamianie w obrębie funkcji Web App for Containers][run-on-web-app-for-containers]

1. Po wyświetleniu okna dialogowego **Run on Web App for Containers** (Uruchamianie w funkcji Web App for Containers) podaj wymagane informacje:

   * **Nazwa**: określa przyjazną nazwę wyświetlaną w zestawie narzędzi platformy Azure. 

   * **Container Registry** (Rejestr kontenerów): z menu rozwijanego wybierz rejestr kontenerów utworzony w poprzedniej sekcji tego artykułu. Pola **adresu URL serwera**, **nazwy użytkownika** i **hasła** zostaną wypełnione automatycznie.

   * **Image and tag** (Obraz i tag): określa nazwę obrazu kontenera; zwykle jest używana następująca składnia: „*rejestr*.azurecr.io/*nazwa_aplikacji*:latest”, gdzie: 
      * *rejestr* to rejestr kontenerów z poprzedniej sekcji tego artykułu 
      * *nazwa_aplikacji* to nazwa aplikacji internetowej 

   * **Use Existing Web App** (Użyj istniejącej aplikacji internetowej) lub **Create New Web App** (Utwórz nową aplikację internetową): określa, czy chcesz wdrożyć kontener w istniejącej aplikacji internetowej, czy też utworzyć nową aplikację internetową. Nazwa aplikacji podana w polu **App name** zostanie użyta do utworzenia adresu URL aplikacji internetowej; na przykład: *wingtiptoys.azurewebsites.net*.

   * **Grupa zasobów**: określa, czy chcesz użyć istniejącej grupy zasobów, czy też utworzyć nową. 

   * **App Service Plan** (Plan usługi App Service): określa, czy chcesz użyć istniejącego planu usługi App Service, czy też utworzyć nowy. 

   ![Uruchamianie w obrębie funkcji Web App for Containers][run-on-web-app-linux]

1. Po zakończeniu konfigurowania wymienionych powyżej ustawień kliknij pozycję **Run** (Uruchom). Po pomyślnym wdrożeniu aplikacji internetowej stan będzie wyświetlany w oknie **Run** (Uruchamianie).

   ![Pomyślnie wdrożona aplikacja internetowa][successfully-deployed]

1. Po opublikowaniu aplikacji internetowej możesz przejść do adresu URL określonego wcześniej dla aplikacji internetowej; na przykład: *wingtiptoys.azurewebsites.net*.

   ![Przechodzenie do aplikacji internetowej][browsing-to-web-app]

## <a name="optional-modify-your-web-app-publish-settings"></a>Opcjonalnie: Modyfikowanie ustawień publikowania aplikacji internetowej

1. Po opublikowaniu aplikacji internetowej ustawienia zostaną zapisane jako domyślne. Aplikację można uruchomić na platformie Azure, klikając ikonę zielonej strzałki na pasku narzędzi. Aby zmodyfikować te ustawienia, można kliknąć menu rozwijane aplikacji internetowej, a następnie kliknąć polecenie **Edit Configurations** (Edytuj konfiguracje).

   ![Menu edytowania konfiguracji][edit-configuration-menu]

1. Po wyświetleniu okna dialogowego **Run/Debug Configurations** (Uruchamianie/debugowanie konfiguracji) można zmodyfikować dowolne ustawienia domyślne, a następnie kliknąć przycisk **OK**.

   ![Okno dialogowe edytowania konfiguracji][edit-configuration-dialog]

## <a name="next-steps"></a>Następne kroki

Dodatkowe zasoby dotyczące platformy Docker znajdują się w oficjalnej [witrynie internetowej platformy Docker][Docker].

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

[Azure Portal]: https://portal.azure.com/
[Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą witryny Azure Portal]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: https://docs.microsoft.com/azure/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[add-docker-support]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/add-docker-support.png
[browsing-to-web-app]:  media/azure-toolkit-for-intellij-hello-world-web-app-linux/browsing-to-web-app.png
[create-container-registry-01]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/create-container-registry-01.png
[create-container-registry-02]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/create-container-registry-02.png
[docker-settings-menu]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/docker-settings-menu.png
[edit-configuration-dialog]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/edit-configuration-dialog.png
[edit-configuration-menu]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/edit-configuration-menu.png
[file-new-project]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/file-new-project.png
[groupid-and-artifactid]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/groupid-and-artifactid.png
[maven-archetype-webapp]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/maven-archetype-webapp.png
[maven-options]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/maven-options.png
[project-name]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/project-name.png
[run-on-web-app-for-containers]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/run-on-web-app-for-containers.png
[run-on-web-app-linux]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/run-on-web-app-linux.png
[successfully-deployed]: media/azure-toolkit-for-intellij-hello-world-web-app-linux/successfully-deployed.png
