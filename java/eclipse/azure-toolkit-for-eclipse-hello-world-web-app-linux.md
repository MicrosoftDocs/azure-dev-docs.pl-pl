---
title: Wdrażanie aplikacji Hello world w kontenerze chmury systemu Linux
titleSuffix: Azure Toolkit for Eclipse
description: Uruchom podstawową aplikację internetową Hello world w kontenerze systemu Linux i wdróż ją w chmurze przy użyciu zestawu narzędzi Azure Toolkit for Eclipse.
services: app-service\web
documentationcenter: java
ms.date: 12/20/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 5ad0949fb415b5bf04e47b8cf6605fe77dbcb0e7
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999543"
---
# <a name="deploy-a-hello-world-web-app-to-a-linux-container-in-the-cloud-using-the-azure-toolkit-for-eclipse"></a>Wdrażanie aplikacji internetowej Hello world w kontenerze systemu Linux w chmurze przy użyciu zestawu narzędzi Azure Toolkit for Eclipse

Użycie kontenerów platformy [Docker] jest powszechnie stosowaną metodą wdrażania aplikacji internetowych. Przy użyciu kontenerów platformy Docker deweloperzy mogą skonsolidować wszystkie pliki projektu i zależności w jednym pakiecie w celu wdrożenia na serwerze. Zestaw narzędzi Azure Toolkit for Eclipse pozwala uprościć ten proces dla deweloperów języka Java dzięki dodaniu funkcji na potrzeby wdrażania kontenerów na platformie Microsoft Azure.

W tym artykule przedstawiono kroki wymagane do utworzenia podstawowej aplikacji internetowej Hello world i opublikowania aplikacji internetowej w kontenerze systemu Linux na platformie Azure przy użyciu zestawu narzędzi Azure Toolkit for Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]
* Klient platformy [Docker].

> [!NOTE]
>
> Aby wykonać kroki opisane w tym samouczku, należy skonfigurować platformę [Docker] do uwidaczniania demona na porcie 2375 bez protokołu TLS. To ustawienie można skonfigurować podczas instalowania platformy Docker lub za pomocą menu ustawień platformy Docker.
>
> ![Menu ustawień platformy Docker][docker-settings-menu]
>

## <a name="create-a-new-web-app-project"></a>Tworzenie projektu nowej aplikacji internetowej

1. Uruchom środowisko Eclipse i zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w artykule [Sign In Instructions for the Azure Toolkit for Eclipse](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-sign-in-instructions) (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for Eclipse).

1. Kliknij menu **File** (Plik), a następnie pozycje **New** (Nowy) i **Dynamic Web Project** (Dynamiczny projekt internetowy).
   
   ![Tworzenie nowego projektu][file-new-project]

1. W oknie dialogowym **New Dynamic Web Project** (Nowy dynamiczny projekt internetowy), określ nazwę i lokalizację projektu, a następnie kliknij pozycję **Finish** (Zakończ).
   
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

1. Po dodaniu obsługi platformy Docker kliknij prawym przyciskiem myszy projekt w eksploratorze projektu, wybierz pozycję **Azure**, a następnie kliknij polecenie **Publish to Web App for Containers** (Publikuj do funkcji Web App for Containers).

   ![Publikowanie do funkcji Web App for Containers][run-on-web-app-for-containers]

1. Po wyświetleniu okna dialogowego **Run on Web App for Containers** (Uruchamianie w funkcji Web App for Containers) podaj wymagane informacje:

   * **Docker File** (Plik platformy Docker): określa ścieżkę do pliku platformy Docker, który został utworzony po dodaniu obsługi platformy Docker do projektu. 

   * **Container Registry** (Rejestr kontenerów): z menu rozwijanego wybierz rejestr kontenerów utworzony w poprzedniej sekcji tego artykułu. Pola **adresu URL serwera**, **nazwy użytkownika** i **hasła** zostaną wypełnione automatycznie.

   * **Image and tag** (Obraz i tag): określa nazwę obrazu kontenera; zwykle jest używana następująca składnia: „*rejestr*.azurecr.io/*nazwa_aplikacji*:latest”, gdzie: 
      * *rejestr* to rejestr kontenerów z poprzedniej sekcji tego artykułu 
      * *nazwa_aplikacji* to nazwa aplikacji internetowej 

   * **Web App for Container** (Aplikacja funkcji Web Apps for Containers): Wybierz pozycję **Use Existing** (Użyj istniejącej) lub **Create New** (Utwórz nową), aby określić, czy chcesz wdrożyć kontener w istniejącej aplikacji internetowej, czy też utworzyć nową aplikację internetową.  Nazwa aplikacji podana w polu **App name** zostanie użyta do utworzenia adresu URL aplikacji internetowej; na przykład: *wingtiptoys.azurewebsites.net*.

   * **Grupa zasobów**: określa, czy chcesz użyć istniejącej grupy zasobów, czy też utworzyć nową. 

   * **App Service Plan** (Plan usługi App Service): określa, czy chcesz użyć istniejącego planu usługi App Service, czy też utworzyć nowy. 

   ![Uruchamianie w funkcji Web App for Containers][run-on-web-app-linux]

1. Po zakończeniu konfigurowania wymienionych powyżej ustawień kliknij przycisk **OK**, aby opublikować aplikację internetową na platformie Azure.

1. Po opublikowaniu aplikacji internetowej możesz przejść do adresu URL określonego wcześniej dla aplikacji internetowej; na przykład: *wingtiptoys.azurewebsites.net*.

   ![Przechodzenie do aplikacji internetowej][browsing-to-web-app]

## <a name="next-steps"></a>Następne kroki

Dodatkowe zasoby dotyczące platformy Docker znajdują się w oficjalnej [witrynie internetowej platformy Docker][Docker].

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Azure Portal]: https://portal.azure.com/
[Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą witryny Azure Portal]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: https://docs.microsoft.com/azure/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[add-docker-support]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/add-docker-support.png
[browsing-to-web-app]:  media/azure-toolkit-for-eclipse-hello-world-web-app-linux/browsing-to-web-app.png
[create-container-registry-01]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/create-container-registry-01.png
[create-container-registry-02]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/create-container-registry-02.png
[docker-settings-menu]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/docker-settings-menu.png
[file-new-project]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/file-new-project.png
[project-name]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/project-name.png
[run-on-web-app-for-containers]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/run-on-web-app-for-containers.png
[run-on-web-app-linux]: media/azure-toolkit-for-eclipse-hello-world-web-app-linux/run-on-web-app-linux.png
