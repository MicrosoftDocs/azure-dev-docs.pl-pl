---
title: Tworzenie aplikacji internetowej Hello world dla platformy Azure w środowisku IntelliJ (starsza wersja)
description: W tym samouczku przedstawiono, jak utworzyć aplikację internetową Hello world dla platformy Azure za pomocą zestawu narzędzi Azure Toolkit for IntelliJ w wersji 3.0.6 (lub starszej).
services: app-service
documentationcenter: java
ms.date: 11/13/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: ffb633ac6d1ddfc4255d6f8917b6a27a5ea0a3bc
ms.sourcegitcommit: db803eba96ffa73b21b94fcb41439cb9b7a0e3c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2019
ms.locfileid: "77000313"
---
# <a name="create-a-hello-world-web-app-for-azure-using-the-legacy-toolkit-for-intellij"></a>Tworzenie aplikacji internetowej Hello world dla platformy Azure przy użyciu starszego zestawu narzędzi dla środowiska IntelliJ

W tym samouczku przedstawiono, jak utworzyć podstawową aplikację Hello world i wdrożyć ją na platformie Azure jako aplikację internetową za pomocą zestawu narzędzi [Azure Toolkit for IntelliJ] w wersji 3.0.6 (lub starszej).

> [!NOTE]
>
> Aby zapoznać się z wersją tego artykułu, w której przedstawiono wykorzystanie zestawu narzędzi [Azure Toolkit for Eclipse], zobacz temat [Tworzenie aplikacji internetowej Hello world dla platformy Azure w środowisku Eclipse][eclipse-hello-world].
>

> [!IMPORTANT]
> 
> Zestaw narzędzi Azure Toolkit for IntelliJ został zaktualizowany w sierpniu 2017 r. przy użyciu innego przepływu pracy. W tym artykule przedstawiono tworzenie aplikacji internetowej Hello world przy użyciu zestawu narzędzi Azure Toolkit for IntelliJ w wersji 3.0.6 (lub starszej). Jeśli używasz zestawu narzędzi w wersji 3.0.7 (lub nowszej), postępuj zgodnie z instrukcjami w temacie [Tworzenie aplikacji internetowej Hello world dla platformy Azure w środowisku IntelliJ][Updated Version].
>

Po wykonaniu instrukcji podanych w tym samouczku, gdy aplikacja będzie wyświetlana w przeglądarce internetowej, będzie wyglądać podobnie jak na poniższej ilustracji:

![Podgląd aplikacji Hello world][01]

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="create-a-new-web-app-project"></a>Tworzenie projektu nowej aplikacji internetowej

1. Uruchom środowisko IntelliJ i zaloguj się do swojego konta Azure, postępując według instrukcji w artykule [Azure Sign In Instructions for the Azure Toolkit for IntelliJ][intelliJ-sign-in-instructions] (Instrukcje logowania na platformie Azure dla zestawu narzędzi Azure Toolkit for IntelliJ).

1. Kliknij menu **File** (Plik), a następnie pozycje **New** (Nowy) i **Project** (Projekt).
   
   ![Opcje Plik, Nowy, Projekt][02]

2. W oknie dialogowym **New Project** (Nowy projekt) wybierz pozycję **Java**, następnie pozycję **Web Application** (Aplikacja internetowa) i kliknij pozycję **New** (Nowy), aby dodać zestaw SDK projektu.
   
   ![Okno dialogowe Nowy projekt][03a]
   
3. W oknie dialogowym Select Home Directory for JDK (Wybierz katalog macierzysty dla zestawu JDK) wybierz folder, w którym zainstalowano zestaw JDK, a następnie kliknij przycisk **OK**. W oknie dialogowym New Project (Nowy projekt) kliknij przycisk **Next** (Dalej), aby kontynuować.
   
   ![Określenie katalogu macierzystego zestawu JDK][03b]

4. Na potrzeby tego samouczka nadaj projektowi nazwę **Java-Web-App-On-Azure**, a następnie kliknij przycisk **Finish** (Zakończ).
   
   ![Okno dialogowe Nowy projekt][04]

5. W widoku Eksploratora projektów w środowisku IntelliJ rozwiń węzeł **Java-Web-App-on-Azure**, rozwiń węzeł **web**, a następnie kliknij dwukrotnie pozycję **index. jsp**.
   
   ![Otwieranie strony indeksu][05c]

6. Gdy plik index.jsp zostanie otwarty w środowisku IntelliJ, dodaj tekst, aby dynamicznie wyświetlać napis **Hello world!** wewnątrz istniejącego elementu `<body>`. Zaktualizowana zawartość elementu `<body>` powinna przypominać następujący przykład:
   
   ```java
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

7. Zapisz plik index.jsp.

## <a name="deploy-your-web-app-to-azure"></a>Wdrażanie aplikacji internetowej na platformie Azure

Aplikację internetową Java można wdrożyć na platformie Azure na kilka sposobów. W tym samouczku opisano jeden z najprostszych: Twoja aplikacja zostanie wdrożona w kontenerze aplikacji internetowych platformy Azure — nie jest wymagany specjalny typ projektu ani dodatkowe narzędzia. Zestaw JDK oraz oprogramowanie kontenera sieci Web zostaną udostępnione przez platformę Azure, więc nie ma potrzeby przekazania własnych — wystarczy aplikacja internetowa Java. Z tego powodu proces publikowania aplikacji zajmie kilka sekund, a nie minut.

Przed opublikowaniem aplikacji należy najpierw skonfigurować ustawienia modułu. Aby to zrobić, wykonaj następujące kroki:

1. W Eksploratorze projektów środowiska IntelliJ kliknij prawym przyciskiem myszy projekt **Java-Web-App-On-Azure**. Po wyświetleniu menu kontekstowego kliknij pozycję **Open Module Settings** (Otwórz ustawienia modułu).

   ![Otwieranie ustawień modułu][05a]

2. Gdy zostanie wyświetlone okno dialogowe Project Structure (Struktura projektu):

   a. Kliknij pozycję **Artifacts** (Artefakty) na liście **Project Settings**.

   b. Zmień nazwę artefaktu w polu **Name** (Nazwa), tak aby nie zawierała spacji ani znaków specjalnych. Jest to konieczne, ponieważ nazwa zostanie użyta w ciągu Uniform Resource Identifier (URI).

   d. Zmień wartość w polu **Type** (Typ) na **Web Application: Archive** (Aplikacja internetowa: Archiwum).

   d. Kliknij przycisk **OK**, aby zamknąć okno dialogowe Project Structure (Struktura projektu).

   ![Otwieranie ustawień modułu][05b]

Po skonfigurowaniu ustawień modułu można opublikować aplikację na platformie Azure, wykonując następujące czynności:

1. W Eksploratorze projektów środowiska IntelliJ kliknij prawym przyciskiem myszy projekt **Java-Web-App-On-Azure**. Po wyświetleniu menu kontekstowego wybierz pozycję **Azure**, a następnie kliknij pozycję **Publish as Azure Web App...** (Opublikuj jako aplikację internetową platformy Azure).
   
   ![Menu kontekstowe publikowania na platformie Azure][06]

2. Jeśli jeszcze nie zostało wykonane logowanie na platformie Azure w środowisku IntelliJ, zostanie wyświetlony monit o zalogowanie się do konta Azure. (Jeśli masz wiele kont Azure, podczas logowania niektóre monity mogą być wyświetlane więcej niż raz, nawet jeśli wydają się być takie same. Jeśli tak się dzieje, postępuj dalej zgodnie z instrukcjami logowania).
   
   ![Okno dialogowe logowania do platformy Azure][07]

3. Po pomyślnym zalogowaniu się do konta platformy Azure w oknie dialogowym **Manage Subscriptions** (Zarządzanie subskrypcjami) zostanie wyświetlona lista subskrypcji skojarzonych z poświadczeniami. (Jeśli na liście znajduje się wiele subskrypcji i chcesz korzystać tylko z określonego ich podzestawu, możesz opcjonalnie usunąć zaznaczenie subskrypcji, których nie chcesz używać). Po wybraniu subskrypcji kliknij przycisk **Close** (Zamknij).
   
   ![Zarządzanie subskrypcjami][08]

4. Gdy zostanie wyświetlone okno dialogowe **Deploy to Azure Web App Container** (Wdrażanie w kontenerze aplikacji internetowych na platformie Azure), będzie zawierać wszystkie utworzone wcześniej kontenery aplikacji internetowych. Jeśli żadne kontenery nie zostały utworzone, lista będzie pusta.
   
   ![Kontenery aplikacji][09]

5. Jeśli nie utworzono jeszcze kontenera aplikacji internetowych na platformie Azure lub jeśli chcesz opublikować aplikację w nowym kontenerze, wykonaj następujące czynności. W przeciwnym razie wybierz istniejący kontener aplikacji internetowych i przejdź do kroku 6 poniżej.
   
   a. Kliknij znak **+** .
      
      ![Dodawanie kontenera aplikacji][10]

   b. Zostanie wyświetlone okno dialogowe **New Web App Container** (Nowy kontener aplikacji internetowych), które będzie używane do następnych kilku kroków.
      
      ![Nowy kontener aplikacji][11a]
   
   d. Wprowadź wartość w polu **DNS Label** (Etykieta DNS) dla kontenera aplikacji internetowych. Spowoduje to utworzenie etykiety DNS liścia adresu URL hosta dla aplikacji internetowych na platformie Azure. Pamiętaj, że nazwa musi być dostępna i zgodna z wymaganiami dotyczącymi nazewnictwa aplikacji internetowych platformy Azure.

   d. W menu rozwijanym **Web Container** (Kontener sieci Web) wybierz odpowiednie oprogramowanie dla aplikacji.
      
      Obecnie możesz wybrać Tomcat 8, Tomcat 7 lub Jetty 9. Najnowsza dystrybucja wybranego oprogramowania zostanie udostępniona przez platformę Azure i będzie uruchamiana na najnowszej dystrybucji zestawu JDK udostępnionej przez platformę Azure.

   e. W menu rozwijanym **Subscription** (Subskrypcja) wybierz subskrypcję, której chcesz użyć do tego wdrożenia.

   f. W menu rozwijanym **Resource Group** (Grupa zasobów) wybierz grupę zasobów, z którą chcesz skojarzyć aplikację internetową. (Grupy zasobów platformy Azure umożliwiają grupowanie powiązanych zasobów ze sobą, dzięki czemu można je razem usuwać).
      
      Możesz wybrać istniejącą grupę zasobów (jeśli masz taką) i przejść do kroku g poniżej lub wykonać następujące czynności w celu utworzenia nowej grupy zasobów:
      
      * Wybierz pozycję **&lt;&lt; Create new Resource Group &gt;&gt;** (Utwórz nową grupę zasobów) w menu rozwijanym **Resource Group** (Grupa zasobów).
      * Zostanie wyświetlone okno dialogowe **New Resource Group** (Nowa grupa zasobów):
        
         ![Nowa grupa zasobów][12]

      * W polu tekstowym **Name** (Nazwa) określ nazwę nowej grupy zasobów.
      * W menu rozwijanym **Region** wybierz odpowiednią lokalizację centrum danych platformy Azure dla tej grupy zasobów.
      * Kliknij przycisk **OK**.

   g. Menu rozwijane **App Service Plan** (Plan usługi App Service) zawiera listę planów usługi App Service skojarzonych z wybraną grupą zasobów. (Plan usługi App Service zawiera takie informacje, jak lokalizacja aplikacji internetowej, warstwa cenowa i rozmiar wystąpienia obliczeniowego. Pojedynczego planu usługi App Service można użyć do wielu aplikacji internetowych, dlatego też jest on obsługiwany niezależnie od określonego wdrożenia aplikacji internetowej).
      
      Możesz wybrać istniejący plan usługi App Service (jeśli masz taki) i przejść do kroku h poniżej lub wykonać następujące czynności w celu utworzenia nowego planu usługi App Service:
      
      * Wybierz pozycję **&lt;&lt; Create new App Service Plan &gt;&gt;** (Utwórz nowy plan usługi App Service) w menu rozwijanym **App Service Plan** (Plan usługi App Service).
      * Zostanie wyświetlone okno dialogowe **New App Service Plan** (Nowy plan usługi App Service).
        
         ![Nowy plan usługi App Service][13]

      * W polu tekstowym **Name** (Nazwa) podaj nazwę nowego planu usługi App Service.
      * W menu rozwijanym **Location** (Lokalizacja) wybierz odpowiednią lokalizację centrum danych platformy Azure dla danego planu.
      * W menu rozwijanym **Pricing Tier** (Warstwa cenowa) wybierz odpowiednie ceny dla planu. W celach testowych możesz wybrać opcję **Free** (Bezpłatny).
      * W menu rozwijanym **Instance Size** (Rozmiar wystąpienia) wybierz odpowiedni rozmiar wystąpienia dla planu. W celach testowych możesz wybrać opcję **Small** (Mały).
      * Kliknij przycisk **OK**.

   h. (Opcjonalnie) Domyślnie jako wirtualna maszyna Java (JVM) na platformie Azure w kontenerze aplikacji internetowych zostanie automatycznie wdrożona ostatnia dystrybucja Java 8. Można jednak wybrać inną wersję i dystrybucję JVM. Aby to zrobić, wykonaj następujące kroki:
      
   * Kliknij kartę **JDK** w oknie dialogowym **New Web App Container** (Nowy kontener aplikacji internetowych).
   * Możesz wybrać jedną z następujących opcji:
        
      * Wdrożenie domyślnego zestawu JDK oferowanego przez platformę Azure
      * Wdrożenie zestawu JDK innej firmy z listy rozwijanej zawierającej dodatkowe zestawy JDK dostępne na platformie Azure
      * Wdrożenie niestandardowego zestawu JDK, który musi być spakowany jako plik ZIP i dostępny publicznie lub na Twoim koncie usługi Azure Storage
        
     ![Karta zestawu JDK nowego kontenera aplikacji][11b]

   i. Po wykonaniu wszystkich powyższych czynności okno dialogowe New Web App Container (Nowy kontener aplikacji internetowych) powinno wyglądać podobnie do przedstawionego na poniższej ilustracji:
      
      ![Nowy kontener aplikacji][14]
   
   j. Kliknij przycisk **OK**, aby zakończyć tworzenie nowego kontenera aplikacji internetowych.
       
      Zaczekaj kilka sekund, aż lista kontenerów aplikacji internetowych zostanie odświeżona. Twój nowo utworzony kontener aplikacji internetowych powinien być teraz wybrany na liście.

6. Teraz możesz przystąpić do zakończenia początkowego wdrożenia aplikacji internetowej na platformie Azure. Kliknij przycisk **OK**, aby wdrożyć aplikację Java w wybranym kontenerze aplikacji internetowych. Domyślnie aplikacja zostanie wdrożona jako podkatalog serwera aplikacji. Jeśli chcesz ją wdrożyć jako aplikację główną, przed kliknięciem przycisku **OK** zaznacz pole wyboru **Deploy to root** (Wdróż do katalogu głównego).
   
   ![Wdrażanie na platformie Azure][15]

7. Następnie powinien zostać wyświetlony widok **Azure Activity Log** (Dziennik aktywności platformy Azure), który będzie zawierać stan wdrażania aplikacji internetowej.
   
   ![Wskaźnik postępu][16]
   
   Proces wdrażania aplikacji internetowej na platformie Azure powinien potrwać nie więcej niż kilka sekund. Gdy aplikacja będzie gotowa, zobaczysz link o nazwie **Published** (Opublikowana) w kolumnie **Status** (Stan). Po kliknięciu linku nastąpi przejście na stronę główną wdrożonej aplikacji internetowej. Możesz też użyć kroków z sekcji poniżej i przejść do aplikacji internetowej.

## <a name="browsing-to-your-web-app-on-azure"></a>Przejście do aplikacji internetowej na platformie Azure

Aby przejść do aplikacji internetowej na platformie Azure, możesz użyć widoku **Azure Explorer**.

Jeśli widok **Azure Explorer** nie jest jeszcze otwarty, możesz go otworzyć, klikając menu **View** (Widok) w środowisku IntelliJ, potem pozycję **Tool Windows** (Okna narzędzi), a następnie pozycję **Service Explorer** (Eksplorator usług). Jeśli wcześniej nie wykonano logowania, zostanie wyświetlony odpowiedni monit.

Gdy zostanie wyświetlony widok **Azure Explorer**, wykonaj następujące czynności, aby przejść do aplikacji internetowej: 

1. Rozwiń węzeł **Azure**.
2. Rozwiń węzeł **Web Apps**. 
3. Kliknij prawym przyciskiem myszy wybraną aplikację internetową.
4. Po wyświetleniu menu kontekstowego kliknij pozycję **Open in Browser** (Otwórz w przeglądarce).
   
   ![Przejście do aplikacji internetowej][17]

## <a name="updating-your-web-app"></a>Aktualizowanie aplikacji internetowej

Aktualizowanie istniejącej uruchomionej aplikacji internetowej na platformie Azure jest szybkie i łatwe. Istnieją dwie metody aktualizowania:

* Można zaktualizować wdrożenie istniejącej aplikacji internetowej Java.
* Można opublikować dodatkową aplikację Java w tym samym kontenerze aplikacji internetowych.

W obu przypadkach proces jest identyczny i trwa tylko kilka sekund:

1. W Eksploratorze projektów środowiska IntelliJ kliknij prawym przyciskiem myszy aplikację Java, którą chcesz zaktualizować lub dodać do istniejącego kontenera aplikacji internetowych.
2. Po wyświetleniu menu kontekstowego wybierz pozycję **Azure**, a następnie **Publish as Azure Web App...** (Opublikuj jako aplikację internetową platformy Azure).
3. Ze względu na to, że logowanie nastąpiło już wcześniej, zostanie wyświetlona lista istniejących kontenerów aplikacji internetowych. Wybierz ten, w którym chcesz opublikować lub ponownie opublikować swoją aplikację Java, a następnie kliknij przycisk **OK**.

Kilka sekund później widok **Azure Activity Log** (Dziennik aktywności platformy Azure) będzie zawierać zaktualizowane wdrożenie oznaczone jako **Published** (Opublikowano) i będziesz mieć możliwość zweryfikowania zaktualizowanej aplikacji w przeglądarce sieci Web.

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>Uruchamianie, zatrzymywanie lub ponowne uruchamianie istniejącej aplikacji internetowej

Aby uruchomić lub zatrzymać istniejący kontener aplikacji internetowych na platformie Azure (w tym wszystkie wdrożone w nim aplikacje języka Java), możesz użyć widoku **Azure Explorer**.

Jeśli widok **Azure Explorer** nie jest jeszcze otwarty, możesz go otworzyć, klikając menu **View** (Widok) w środowisku IntelliJ, potem pozycję **Tool Windows** (Okna narzędzi), a następnie pozycję **Service Explorer** (Eksplorator usług). Jeśli wcześniej nie wykonano logowania, zostanie wyświetlony odpowiedni monit.

Gdy zostanie wyświetlony widok **Azure Explorer**, wykonaj następujące czynności, aby uruchomić lub zatrzymać aplikację internetową: 

1. Rozwiń węzeł **Azure**.
2. Rozwiń węzeł **Web Apps**. 
3. Kliknij prawym przyciskiem myszy wybraną aplikację internetową.
4. Po wyświetleniu menu kontekstowego kliknij pozycję **Start** (Uruchom), **Stop** (Zatrzymaj) lub **Restart** (Uruchom ponownie). Pamiętaj, że opcje menu są kontekstowe, więc można zatrzymać tylko uruchomioną aplikację internetową lub uruchomić aplikację internetową, która nie jest aktualnie uruchomiona.
   
   ![Zatrzymywanie aplikacji internetowej][18]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

Aby uzyskać dodatkowe informacje o tworzeniu aplikacji internetowych na platformie Azure [Omówienie usługi Web Apps] (Omówienie usługi Web Apps).

<!-- URL List -->

[Azure Toolkit for IntelliJ]: azure-toolkit-for-intellij.md (Zestaw narzędzi platformy Azure dla środowiska IntelliJ)
[Azure Toolkit for Eclipse]: ../eclipse/azure-toolkit-for-eclipse.md
[eclipse-hello-world]: ../eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app.md
[Omówienie usługi Web Apps]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Updated Version]: azure-toolkit-for-intellij-create-hello-world-web-app.md
[intelliJ-sign-in-instructions]: azure-toolkit-for-intellij-sign-in-instructions.md

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/01-Web-Page.png
[02]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/02-File-New-Project.png
[03a]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/03-New-Project-Dialog.png
[03b]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/03-New-Project-SDK-Dialog.png
[04]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/04-New-Project-Dialog.png
[05a]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/05-Open-Module-Settings.png
[05b]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/05-Project-Structure-Dialog.png
[05c]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/05-Open-Index-Page.png
[06]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/06-Azure-Publish-Context-Menu.png
[07]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/07-Azure-Log-In-Dialog.png
[08]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/08-Manage-Subscriptions.png
[09]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/09-App-Containers.png
[10]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/10-Add-App-Container.png
[11a]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/11-New-App-Container.png
[11b]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/11-New-App-Container-JDK-Tab.png
[12]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/12-New-Resource-Group.png
[13]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/13-New-App-Service-Plan.png
[14]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/14-New-App-Container.png
[15]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/15-Deploy-To-Azure.png
[16]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/16-Progress-Indicator.png
[17]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/17-Browse-Web-App.png
[18]: ./media/azure-toolkit-for-intellij-create-hello-world-web-app-legacy-version/18-Stop-Web-App.png
