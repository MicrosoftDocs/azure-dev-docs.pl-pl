---
title: Tworzenie aplikacji internetowej Hello world dla usługi Azure App Service przy użyciu platformy IntelliJ
description: W tym samouczku pokazano, jak utworzyć aplikację internetową Hello world dla platformy Azure za pomocą zestawu narzędzi Azure Toolkit for IntelliJ.
services: app-service
keywords: java, intellij, aplikacja internetowa, azure app service, hello world, szybki start
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 21c680281de231cff7c3f2f2044c63383c5a0e4c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999473"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>Tworzenie aplikacji internetowej Hello world dla usługi Azure App Service przy użyciu platformy IntelliJ

Korzystając z wtyczki [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) typu open source, można w kilka minut utworzyć i wdrożyć podstawową aplikację Hello world do Azure App Service jako aplikację internetową.

> [!NOTE]
>
> Jeśli wolisz korzystać ze środowiska Eclipse, zapoznaj się z przygotowanym przez nas [podobnym samouczkiem dotyczącym środowiska Eclipse][eclipse-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](../includes/quickstarts-free-trial-note.md)]
>
> Nie zapomnij wyczyścić zasobów po zakończeniu pracy z tym samouczkiem. W takim przypadku uruchomienie tego przewodnika nie spowoduje przekroczenia limitu przydziału bezpłatnego konta.
>

[!INCLUDE [azure-toolkit-for-intellij-basic-prerequisites](../includes/azure-toolkit-for-intellij-basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Instalacja i logowanie

1. W oknie dialogowym Settings/Preferences (Ustawienia/preferencje) (Ctrl+Alt+S) środowiska IntelliJ IDEA wybierz pozycję **Plugins** (Wtyczki). Następnie znajdź pozycję **Azure Toolkit for IntelliJ** w witrynie **Marketplace** i kliknij pozycję **Install** (Zainstaluj). Po zainstalowaniu kliknij przycisk **Restart** (Uruchom ponownie), aby aktywować wtyczkę. 

   ![Wtyczka Azure Toolkit for IntelliJ w portalu Marketplace][marketplace]

2. Aby zalogować się do konta platformy Azure, otwórz pasek boczny programu **Azure Explorer** i kliknij ikonę **Azure Sign In** (Logowanie do platformy Azure) w górnej części paska (lub z menu IDEA **Tools/Azure/Azure Sign in** [Narzędzia/Azure/Logowanie Azure]).

   ![Polecenie Azure Sign In (Logowanie do platformy Azure) na platformie IntelliJ][I01]

3. W oknie **Azure Sign In** (Logowanie do platformy Azure) wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj się) ([inne opcje logowania](azure-toolkit-for-intellij-sign-in-instructions.md)).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][I02]

4. Kliknij pozycję **Copy&Open** (Kopiuj i otwórz) w oknie dialogowym **Azure Device Login** (Logowanie do urządzenia platformy Azure).

   ![Okno dialogowe logowania do platformy Azure][I03]

5. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania do urządzenia][I04]

6. W oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][I05]

## <a name="creating-web-app-project"></a>Tworzenie projektu aplikacji internetowej

1. Na platformie IntelliJ kliknij menu **File** (Plik), a następnie kliknij kolejno pozycje **New** (Nowy) i **Project** (Projekt).

   ![Tworzenie nowego projektu][file-new-project]

2. W oknie dialogowym **New Project** (Nowy projekt) wybierz kolejno pozycje **Maven** i **maven-archetype-webapp**, a następnie kliknij pozycję **Next** (Dalej).

   ![Wybieranie aplikacji internetowej archetypu programu Maven][maven-archetype-webapp]

3. Określ wartości **GroupId** (Identyfikator grupy) i **ArtifactId** (Identyfikator artefaktu) dla aplikacji internetowej, a następnie kliknij przycisk **Next** (Dalej).

   ![Określanie wartości GroupId (Identyfikator grupy) i ArtifactId (Identyfikator artefaktu)][groupid-and-artifactid]

4. Dostosuj ustawienia programu Maven lub zaakceptuj wartości domyślne, a następnie kliknij przycisk **Next** (Dalej).

   ![Określanie ustawień programu Maven][maven-options]

5. Określ nazwę i lokalizację projektu, a następnie kliknij pozycję **Finish** (Zakończ).

   ![Określanie nazwy projektu][project-name]

6. W widoku Project Explorer (Eksplorator projektu) otwórz i edytuj plik **src/main/webapp/index.jsp** w następujący sposób i **zapisz zmiany**:

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```

   ![Edytowanie strony indeksu][edit-index-page]

## <a name="deploying-web-app-to-azure"></a>Wdrażanie aplikacji internetowej na platformie Azure

1. W widoku Project Explorer (Eksplorator projektu) kliknij prawym przyciskiem myszy projekt, rozwiń węzeł **Azure**, a następnie kliknij pozycję **Deploy to Azure** (Wdróż na platformie Azure).

   ![Menu Deploy to Azure (Wdróż na platformie Azure)][deploy-to-azure-menu]

1. W oknie dialogowym Deploy to Azure (Wdrażanie na platformie Azure) możesz bezpośrednio wdrożyć aplikację w istniejącej aplikacji internetowej Tomcat, jeśli już istnieje. W przeciwnym razie należy najpierw utworzyć nową.
   1. Kliknij link **No Available webapp, click to create a new one** (Brak dostępnej aplikacji internetowej, kliknij w celu utworzenia nowej), aby utworzyć nową aplikację internetową. Jeśli w subskrypcji istnieją aplikacje internetowe, możesz wybrać pozycję **Create New WebApp** (Utwórz nową aplikację internetową) z listy rozwijanej WebApp (Aplikacja internetowa).

      ![Okno dialogowe Deploy to Azure (Wdrażanie na platformie Azure)][deploy-to-azure-dialog]

   1. W podręcznym oknie dialogowym wybierz pozycję **TOMCAT 8.5-jre8** jako kontener internetowy i podaj inne wymagane informacje, a następnie kliknij przycisk **OK**, aby utworzyć aplikację internetową.

      ![Tworzenie nowej aplikacji internetowej][create-new-web-app-dialog]

   1. Wybierz aplikację internetową z listy rozwijanej WebApp (Aplikacja internetowa), a następnie kliknij przycisk **Run** (Uruchom). (Możesz zacząć od tego miejsca, jeśli chcesz wdrażać w istniejącej aplikacji internetowej)

      ![Wdrażanie w istniejącej aplikacji internetowej][deploy-to-existing-webapp]

1. Po pomyślnym wdrożeniu aplikacji internetowej zestaw narzędzi wyświetli komunikat o stanie wraz z adresem URL tej aplikacji.

   ![Pomyślne wdrożenie][successfully-deployed]

1. Aby przejść do aplikacji internetowej, możesz użyć linku dostępnego w komunikacie o stanie.

   ![Przechodzenie do aplikacji internetowej][browse-web-app]

## <a name="managing-deploy-configurations"></a>Zarządzanie konfiguracjami wdrażania

1. Po opublikowaniu aplikacji internetowej ustawienia zostaną zapisane jako domyślne. Wdrożenie można uruchomić, klikając ikonę zielonej strzałki na pasku narzędzi. Aby zmodyfikować ustawienia, można kliknąć menu rozwijane aplikacji internetowej, a następnie kliknąć polecenie **Edit Configurations** (Edytuj konfiguracje).

   ![Menu edytowania konfiguracji][edit-configuration-menu]

1. Po wyświetleniu okna dialogowego **Run/Debug Configurations** (Uruchamianie/debugowanie konfiguracji) można zmodyfikować dowolne ustawienia domyślne, a następnie kliknąć przycisk **OK**.

   ![Okno dialogowe edytowania konfiguracji][edit-configuration-dialog]

## <a name="cleaning-up-resources"></a>Oczyszczanie zasobów

1. Usuwanie aplikacji internetowych w programie Azure Explorer

     ![Czyszczenie zasobów][clean-resources]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

Aby uzyskać dodatkowe informacje o tworzeniu funkcji Azure Web Apps, zobacz temat [Web Apps Overview] (Omówienie usługi Web Apps).

<!-- URL List -->

[Azure Toolkit for IntelliJ]: azure-toolkit-for-intellij.md
[Azure Toolkit for Eclipse]: ../eclipse/azure-toolkit-for-eclipse.md
[eclipse-hello-world]: ../eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app.md
[Web Apps Overview]: /azure/app-service/app-service-web-overview (Omówienie usługi Web Apps)
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version.md
[intelliJ-sign-in-instructions]: azure-toolkit-for-intellij-sign-in-instructions.md

<!-- IMG List -->
[marketplace]:./media/azure-toolkit-for-intellij-create-hello-world-web-app/marketplace.png
[file-new-project]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/maven-options.png
[project-name]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/project-name.png
[open-index-page]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/open-index-page.png
[edit-index-page]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/edit-index-page.png
[deploy-to-azure-menu]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/run-on-web-app-menu.png
[deploy-to-azure-dialog]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/run-on-web-app-dialog.png
[deploy-to-existing-webapp]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/deploy-to-existing-webapp.png
[create-new-web-app-dialog]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/edit-configuration-dialog.png
[clean-resources]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app/clean-resource.png
[I01]: media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-intellij-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-intellij-sign-in-instructions/I05.png
