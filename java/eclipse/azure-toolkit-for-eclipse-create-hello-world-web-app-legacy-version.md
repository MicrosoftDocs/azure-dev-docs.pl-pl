---
title: Tworzenie aplikacji internetowej Hello world dla platformy Azure na platformie Eclipse (w starszym zestawie narzędzi)
description: W tym samouczku przedstawiono, jak utworzyć aplikację internetową Hello world dla platformy Azure za pomocą zestawu narzędzi Azure Toolkit for Eclipse w wersji 3.0.6 lub starszej.
services: app-service
documentationcenter: java
ms.date: 11/13/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 877c42b4277fe2bf9c55d276cb3f53a66747c69d
ms.sourcegitcommit: db803eba96ffa73b21b94fcb41439cb9b7a0e3c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2019
ms.locfileid: "77000306"
---
# <a name="create-a-hello-world-web-app-for-azure-using-the-legacy-toolkit-for-eclipse"></a>Tworzenie aplikacji internetowej Hello world dla platformy Azure przy użyciu starszego zestawu narzędzi dla środowiska Eclipse

W tym samouczku przedstawiono, jak utworzyć podstawową aplikację Hello world i wdrożyć ją na platformie Azure jako aplikację internetową za pomocą zestawu narzędzi [Azure Toolkit for Eclipse] w wersji 3.0.6 lub starszej.

> [!NOTE]
>
> Wersja niniejszego artykułu, w której omówiono użycie zestawu [Azure Toolkit for IntelliJ], nosi tytuł [Create a Hello World web app for Azure using IntelliJ][intellij-hello-world] (Tworzenie aplikacji internetowej Hello world dla platformy Azure w środowisku IntelliJ).
>

> [!IMPORTANT]
> 
> Zestaw narzędzi Azure Toolkit for Eclipse został zaktualizowany w sierpniu 2017 r. i ma teraz inny przepływ pracy. W tym artykule przedstawiono tworzenie aplikacji internetowej Hello world przy użyciu zestawu narzędzi Azure Toolkit for Eclipse w wersji 3.0.6 lub starszej. Jeśli używasz zestawu narzędzi w wersji 3.0.7 lub nowszej, postępuj zgodnie z instrukcjami w temacie [Create a Hello World web app for Azure in Eclipse][Updated Version] (Tworzenie aplikacji internetowej Hello world dla platformy Azure w środowisku Eclipse).
>

Po wykonaniu instrukcji podanych w tym samouczku aplikacja wyświetlana w przeglądarce internetowej będzie wyglądać podobnie jak na poniższej ilustracji:

![Podgląd aplikacji Hello world][01]

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="create-a-new-web-app-project"></a>Tworzenie projektu nowej aplikacji internetowej

1. Uruchom środowisko Eclipse i zaloguj się do swojego konta Azure, postępując według instrukcji w artykule [Azure Sign In Instructions for the Azure Toolkit for Eclipse][eclipse-sign-in-instructions] (Instrukcje logowania na platformie Azure dla zestawu narzędzi Azure Toolkit for Eclipse).

1. Kliknij pozycję **File** (Plik), kliknij polecenie **New** (Nowy), po czym kliknij polecenie **Dynamic Web Project** (Dynamiczny projekt sieci Web). Jeśli nie widzisz polecenia **Dynamic Web Project** (Dynamiczny projekt sieci Web) na liście dostępnych projektów po kliknięciu poleceń **File** (Plik) i **New** (Nowy), w menu **File** (Plik) kliknij polecenie **New** (Nowy), kliknij element **Project...** (Projekt), rozwiń pozycję **Web** (Sieć Web), kliknij pozycję **Dynamic Web Project** (Dynamiczny projekt sieci Web) i kliknij przycisk **Next** (Dalej).

2. Na potrzeby tego samouczka nazwij projekt **MyWebApp**. Wyświetlony ekran powinien wyglądać podobnie do następującego:
   
   ![Tworzenie dynamicznego projektu sieci Web][02]

3. Kliknij przycisk **Zakończ**.

4. W widoku **Project Explorer**r (Eksplorator projektu) w środowisku Eclipse rozwiń węzeł **MyWebApp**. Kliknij prawym przyciskiem myszy folder **WebContent**, kliknij polecenie **New** (Nowy), a następnie kliknij polecenie **JSP File** (Plik JSP).

5. W oknie dialogowym **New JSP File** (Nowy plik JSP) podaj nazwę pliku **index.jsp**, pozostaw folder **MyWebApp/WebContent** jako folder nadrzędny, a następnie kliknij przycisk **Next** (Dalej).

6. W oknie dialogowym **Select JSP Template** (Wybierz szablon JSP) na potrzeby tego samouczka wybierz pozycję **New JSP File (html)** (Nowy plik JSP (html)), a następnie kliknij **Finish** (Zakończ).

7. Gdy plik index.jsp zostanie otwarty w środowisku Eclipse, dodaj tekst, aby dynamicznie wyświetlać napis **Hello world!** wewnątrz istniejącego elementu `<body>`. Zaktualizowana zawartość elementu `<body>` powinna przypominać następujący przykład:
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. Save index.jsp.

## <a name="deploy-your-web-app-to-azure"></a>Wdrażanie aplikacji internetowej na platformie Azure

Aplikację internetową Java można wdrożyć na platformie Azure na kilka sposobów. W tym samouczku opisano jeden z najprostszych: Twoja aplikacja zostanie wdrożona w kontenerze aplikacji internetowej platformy Azure — nie jest wymagany specjalny typ projektu ani dodatkowe narzędzia. Zestaw JDK oraz oprogramowanie kontenera sieci Web zostaną udostępnione przez platformę Azure, więc nie ma potrzeby przekazania własnych — wystarczy aplikacja internetowa Java. Z tego powodu proces publikowania aplikacji zajmie kilka sekund, a nie minut.

1. W widoku Project Explorer (Eksplorator projektu) platformy Eclipse kliknij prawym przyciskiem myszy aplikację **MyWebApp**.

2. Po wyświetleniu menu kontekstowego wybierz pozycję **Azure**, a następnie kliknij pozycję **Publish as Azure Web App...** (Opublikuj jako aplikację internetową platformy Azure).
   
   ![Pozycja Publish as Azure Web App... (Opublikuj jako aplikację internetową platformy Azure)][03]
   
   Po wybraniu projektu aplikacji sieci Web w widoku Project Explorer (Eksplorator projektu) możesz też kliknąć przycisk listy rozwijanej **Publish** (Opublikuj) na pasku narzędzi i wybrać z listy pozycję **Publish as Azure Web App** (Opublikuj jako aplikację internetową platformy Azure):
   
   ![Pozycja Publish as Azure Web App... (Opublikuj jako aplikację internetową platformy Azure)][14]

3. Jeśli jeszcze nie zalogowano się na koncie platformy Azure w środowisku Eclipse, zostanie wyświetlony monit o zalogowanie się:
   
   ![Okno dialogowe logowania do platformy Azure][04]
   
   Jeśli masz wiele kont Azure, podczas logowania niektóre monity mogą być wyświetlane więcej niż raz, nawet jeśli wydają się takie same. Jeśli tak się dzieje, postępuj dalej zgodnie z instrukcjami logowania.

4. Po pomyślnym zalogowaniu się do konta platformy Azure w oknie dialogowym **Manage Subscriptions** (Zarządzanie subskrypcjami) zostanie wyświetlona lista subskrypcji skojarzonych z poświadczeniami. Jeśli na liście znajduje się wiele subskrypcji i chcesz korzystać tylko z określonego ich podzestawu, możesz opcjonalnie usunąć zaznaczenie subskrypcji, których nie chcesz używać. Po wybraniu subskrypcji kliknij przycisk **Close** (Zamknij).
   
   ![Okno dialogowe Manage Subscriptions (Zarządzanie subskrypcjami)][05]

5. Wyświetlone okno dialogowe **Deploy to Azure Web App Container** (Wdrażanie w kontenerze aplikacji internetowej na platformie Azure) będzie zawierać wszystkie utworzone wcześniej kontenery aplikacji internetowych; jeśli żadne kontenery nie zostały utworzone, lista będzie pusta.
   
   ![Okno dialogowe Deploy to Azure Web App Container (Wdrażanie w kontenerze aplikacji internetowej na platformie Azure)][06]

6. Jeśli nie utworzono jeszcze kontenera aplikacji internetowej na platformie Azure lub jeśli chcesz opublikować aplikację w nowym kontenerze, wykonaj następujące czynności. W przeciwnym razie wybierz istniejący kontener aplikacji internetowej i przejdź do kroku 7 poniżej.
   
   a. Kliknij pozycję **New...** (Nowy).
      
      ![Okno dialogowe Deploy to Azure Web App Container (Wdrażanie w kontenerze aplikacji internetowej na platformie Azure)][15]

   b. Zostanie wyświetlone okno dialogowe **New Web App Container** (Nowy kontener aplikacji internetowej):
      
      ![Okno dialogowe New Web App Container (Nowy kontener aplikacji internetowej)][07a]

   d. Wprowadź wartość w polu **DNS Label** (Etykieta DNS) dla kontenera aplikacji internetowej; spowoduje to utworzenie etykiety DNS liścia adresu URL hosta dla aplikacji internetowej na platformie Azure. Pamiętaj, że nazwa musi być dostępna i zgodna z wymaganiami dotyczącymi nazewnictwa aplikacji internetowych platformy Azure.

   d. W menu rozwijanym **Web Container** (Kontener sieci Web) wybierz odpowiednie oprogramowanie dla aplikacji.
      
      Obecnie możesz wybrać Tomcat 8, Tomcat 7 lub Jetty 9. Najnowsza dystrybucja wybranego oprogramowania zostanie udostępniona przez platformę Azure i będzie uruchamiana na najnowszej dystrybucji zestawu JDK udostępnionej przez platformę Azure.

   e. W menu rozwijanym **Subscription** (Subskrypcja) wybierz subskrypcję, której chcesz użyć dla tego wdrożenia.

   f. W menu rozwijanym **Resource Group** (Grupa zasobów) wybierz grupę zasobów, z którą chcesz skojarzyć aplikację internetową. (Grupy zasobów platformy Azure umożliwiają grupowanie powiązanych zasobów ze sobą, dzięki czemu można je na przykład razem usuwać).
      
      Możesz wybrać istniejącą grupę zasobów (jeśli istnieje) i przejść do kroku g poniżej lub wykonać następujące czynności w celu utworzenia nowej grupy zasobów:
      
   * Kliknij pozycję **New...** (Nowy).
   * Zostanie wyświetlone okno dialogowe **New Resource Group** (Nowa grupa zasobów):
        
       ![Okno dialogowe New Resource Group (Nowa grupa zasobów)][08]
   * W polu tekstowym **Name** (Nazwa) określ nazwę nowej grupy zasobów.
   * W menu rozwijanym **Region** wybierz odpowiednią lokalizację centrum danych platformy Azure dla danej grupy zasobów.
   * OPCJONALNIE: Domyślnie ostatnia dystrybucja Java 8 zostanie automatycznie wdrożona jako środowisko JVM przez platformę Azure do kontenera aplikacji internetowej. Można jednak określić inną wersję i dystrybucję środowiska JVM, jeśli wymaga tego aplikacja internetowa. Aby określić środowisko JDK dla aplikacji internetowej, kliknij kartę **JDK** i wybierz jedną z poniższych opcji:
     * **Deploy the default JDK offered by Azure Web Apps service** (Wdróż domyślny zestaw JDK oferowany przez aplikacje Azure Web Apps): Zostanie wdrożona najnowsza dystrybucja języka Java.
     * **Deploy a 3rd party JDK available on Azure** (Wdróż zestaw JDK innej firmy dostępny na platformie Azure): Umożliwia wybór z listy zestawów JDK dostępnych na platformie Microsoft Azure.
     * **Deploy my own JDK from this download location** (Wdróż mój własny zestaw JDK z tej lokalizacji pobierania): Umożliwia określenie własnej dystrybucji zestawu JDK, która musi być spakowana w pliku ZIP i przekazana do publicznie dostępnej lokalizacji pobierania lub na konto magazynu Azure, do którego masz dostęp.
          
       ![Okno dialogowe New Web App Container (Nowy kontener aplikacji internetowej)][07b]

   g. Kliknij przycisk **OK**.

   h. Menu rozwijane **App Service Plan** (Plan usługi App Service) zawiera listę planów usługi App Service skojarzonych z wybraną grupą zasobów. (Plany usługi App Service określają informacje takie jak lokalizacja aplikacji internetowej, warstwę cenową i rozmiar wystąpienia obliczeniowego). Pojedynczego planu usługi App Service można użyć do wielu aplikacji internetowych, dlatego też jest on obsługiwany niezależnie od określonego wdrożenia aplikacji internetowej).
      
       You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:
      
      * Kliknij pozycję **New...** (Nowy).
      * Zostanie wyświetlone okno dialogowe **New App Service Plan** (Nowy plan usługi App Service):
        
          ![Okno dialogowe New App Service Plan (Nowy plan usługi App Service)][09]
      * W polu tekstowym **Name** (Nazwa) podaj nazwę nowego planu usługi App Service.
      * W menu rozwijanym **Location** (Lokalizacja) wybierz odpowiednią lokalizację centrum danych platformy Azure dla danego planu.
      * W menu rozwijanym **Pricing Tier** (Warstwa cenowa) wybierz odpowiednie ceny dla planu. W celach testowych możesz wybrać opcję **Free** (Bezpłatny).
      * W menu rozwijanym **Instance Size** (Rozmiar wystąpienia) wybierz odpowiedni rozmiar wystąpienia dla planu. W celach testowych możesz wybrać opcję **Small** (Mały).

   i. Po wykonaniu wszystkich powyższych czynności okno dialogowe New Web App Container (Nowy kontener aplikacji internetowej) powinno wyglądać podobnie do przedstawionego na poniższej ilustracji:
      
      ![Okno dialogowe New Web App Container (Nowy kontener aplikacji internetowej)][10]

   j. Kliknij przycisk **OK**, aby ukończyć tworzenie nowego kontenera aplikacji internetowej.
       
      Zaczekaj kilka sekund na odświeżenie listy kontenerów aplikacji internetowej. Twój nowo utworzony kontener aplikacji internetowej powinien być teraz wybrany na liście.

7. Możesz teraz ukończyć wstępne wdrożenie aplikacji internetowej na platformie Azure:
   
   ![Okno dialogowe Deploy to Azure Web App Container (Wdrażanie w kontenerze aplikacji internetowej na platformie Azure)][11]
   
   Kliknij przycisk **OK**, aby wdrożyć aplikację Java w wybranym kontenerze aplikacji internetowej.
   
   Domyślnie aplikacja zostanie wdrożona jako podkatalog serwera aplikacji. Jeśli chcesz ją wdrożyć jako aplikację główną, przed kliknięciem przycisku **OK** zaznacz pole wyboru **Deploy to root** (Wdróż do katalogu głównego).

8. Następnie powinien zostać wyświetlony widok **Azure Activity Log** (Dziennik aktywności platformy Azure) zawierający stan wdrażania aplikacji internetowej.
   
   ![Dziennik aktywności platformy Azure][12]
   
   Proces wdrażania aplikacji internetowej na platformie Azure powinien potrwać nie więcej niż kilka sekund. Gdy aplikacja będzie gotowa, zobaczysz link o nazwie **Published** (Opublikowana) w kolumnie **Status** (Stan). Po jego kliknięciu nastąpi przejście do strony głównej wdrożonej aplikacji internetowej.

## <a name="updating-your-web-app"></a>Aktualizowanie aplikacji internetowej

Aktualizowanie istniejącej uruchomionej aplikacji internetowej na platformie Azure jest szybkie i łatwe. Istnieją dwie metody aktualizowania:

* Można zaktualizować wdrożenie istniejącej aplikacji internetowej Java.
* Można opublikować dodatkową aplikację Java w tym samym kontenerze aplikacji internetowej.

W obu przypadkach proces jest identyczny i trwa tylko kilka sekund:

1. W Eksploratorze projektów środowiska Eclipse kliknij prawym przyciskiem myszy aplikację Java, którą chcesz zaktualizować lub dodać do istniejącego kontenera aplikacji internetowej.

2. Po wyświetleniu menu kontekstowego wybierz pozycję **Azure**, a następnie **Publish as Azure Web App...** (Opublikuj jako aplikację internetową platformy Azure).

3. Ponieważ logowanie nastąpiło już wcześniej, zostanie wyświetlona lista istniejących kontenerów aplikacji internetowej. Wybierz ten, w którym chcesz opublikować lub ponownie opublikować swoją aplikację Java, a następnie kliknij przycisk **OK**.

Kilka sekund później widok **Azure Activity Log** (Dziennik aktywności platformy Azure) będzie zawierać zaktualizowane wdrożenie oznaczone jako **Published** (Opublikowana) i będziesz mieć możliwość zweryfikowania zaktualizowanej aplikacji w przeglądarce sieci Web.

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>Uruchamianie, zatrzymywanie lub ponowne uruchamianie istniejącej aplikacji internetowej

Aby uruchomić lub zatrzymać istniejący kontener aplikacji internetowej platformy Azure (w tym wszystkie wdrożone w nim aplikacje języka Java), możesz użyć widoku **Azure Explorer** (Eksplorator Azure).

Jeśli widok **Azure Explorer** (Eksplorator Azure) nie jest jeszcze otwarty, możesz go otworzyć, klikając menu **Window** (Okno) w środowisku Eclipse, pozycje **Show View** (Wyświetl widok), **Other...** (Inny), **Azure**, a następnie klikając pozycję **Azure Explorer** (Eksplorator Azure). Jeśli jeszcze nie nastąpiło logowanie, zostanie wyświetlony odpowiedni monit.

Gdy zostanie wyświetlony widok **Azure Explorer** (Eksplorator Azure), wykonaj następujące czynności, aby uruchomić lub zatrzymać aplikację internetową: 

1. Rozwiń węzeł **Azure**.

2. Rozwiń węzeł **Web Apps** (Aplikacje internetowe). 

3. Kliknij prawym przyciskiem myszy wybraną aplikację internetową.

4. Po wyświetleniu menu kontekstowego kliknij opcję **Start** (Uruchom), **Stop** (Zatrzymaj) lub **Restart** (Uruchom ponownie). Pamiętaj, że opcje menu są kontekstowe, więc można zatrzymać tylko uruchomioną aplikację internetową lub uruchomić aplikację internetową, która nie jest aktualnie uruchomiona.
   
   ![Zatrzymywanie istniejącej aplikacji internetowej][13]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

Aby uzyskać dodatkowe informacje o tworzeniu aplikacji internetowych na platformie Azure, zobacz artykuł [Omówienie usługi App Service].

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../intellij/azure-toolkit-for-intellij.md (Zestaw narzędzi platformy Azure dla środowiska IntelliJ)
[intellij-hello-world]: ../intellij/azure-toolkit-for-intellij-create-hello-world-web-app.md
[Omówienie usługi App Service]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Updated Version]: azure-toolkit-for-eclipse-create-hello-world-web-app.md
[eclipse-sign-in-instructions]: azure-toolkit-for-eclipse-sign-in-instructions.md


<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/01-Web-Page.png
[02]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/02-Dynamic-Web-Project.png
[03]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/03-Context-Menu.png
[04]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/04-Log-In-Dialog.png
[05]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/05-Manage-Subscriptions-Dialog.png
[06]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/07b-New-Web-App-Container-Dialog.png
[08]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/08-New-Resource-Group-Dialog.png
[09]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/09-New-Service-Plan-Dialog.png
[10]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/11-Completed-Deploy-Dialog.png
[12]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/12-Activity-Log-View.png
[13]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/13-Azure-Explorer-Web-App.png
[14]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/14-publishDropdownButton.png
[15]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version/15-New-Azure-Web-Container.png
