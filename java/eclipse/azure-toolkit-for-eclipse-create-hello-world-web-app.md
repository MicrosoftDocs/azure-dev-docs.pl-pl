---
title: Tworzenie aplikacji internetowej Hello world dla usługi Azure App Service przy użyciu środowiska Eclipse
description: W tym samouczku pokazano, jak utworzyć aplikację internetową Hello world dla platformy Azure za pomocą zestawu narzędzi Azure Toolkit for Eclipse.
services: app-service
keywords: java, eclipse, aplikacja internetowa, azure app service, hello world, szybki start
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: ae3d983a0b3e64489fa89d0e6a845310878b204c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999564"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Tworzenie aplikacji internetowej Hello world dla usługi Azure App Service przy użyciu środowiska Eclipse

Korzystając z wtyczki [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) typu open source, można w kilka minut utworzyć i wdrożyć podstawową aplikację Hello world do Azure App Service jako aplikację internetową.

> [!NOTE]
>
> Jeśli wolisz korzystać ze środowiska IntelliJ IDEA, zapoznaj się z przygotowanym przez nas [podobnym samouczkiem dotyczącym platformy IntelliJ][intellij-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](../includes/quickstarts-free-trial-note.md)]
>
> Nie zapomnij wyczyścić zasobów po zakończeniu pracy z tym samouczkiem. W takim przypadku uruchomienie tego przewodnika nie spowoduje przekroczenia limitu przydziału bezpłatnego konta.
>

[!INCLUDE [azure-toolkit-for-intellij-basic-prerequisites](../includes/azure-toolkit-for-eclipse-basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Instalacja i logowanie

1. Przeciągnij poniższy przycisk do uruchomionego obszaru roboczego środowiska Eclipse, aby zainstalować wtyczkę Azure Toolkit for Eclipse ([inne opcje instalacji](azure-toolkit-for-eclipse-installation.md)).

    [![Przeciągnij do działającego obszaru roboczego środowiska Eclipse*. *Wymaga rozwiązania Eclipse Marketplace Client](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Przeciągnij do działającego obszaru roboczego środowiska Eclipse*. *Wymaga rozwiązania Eclipse Marketplace Client")

1. Aby zalogować się do konta platformy Azure, kliknij pozycję **Tools** (Narzędzia), kliknij pozycję **Azure**, a następnie kliknij pozycję **Sign In** (Zaloguj się).
   ![Menu środowiska Eclipse na potrzeby logowania do platformy Azure][I01]

1. W oknie **Azure Sign In** (Logowanie do platformy Azure) wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj się) ([inne opcje logowania](azure-toolkit-for-eclipse-sign-in-instructions.md)).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][I02]

1. Kliknij pozycję **Copy&Open** (Kopiuj i otwórz) w oknie dialogowym **Azure Device Login** (Logowanie do urządzenia platformy Azure).

   ![Okno dialogowe logowania do platformy Azure][I03]

1. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania do urządzenia][I04]

1. Na koniec w oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][I05]

## <a name="creating-web-app-project"></a>Tworzenie projektu aplikacji internetowej

1. Kliknij pozycję **File** (Plik), kliknij polecenie **New** (Nowy), a potem kliknij polecenie **Dynamic Web Project** (Dynamiczny projekt internetowy). (Jeśli nie widzisz polecenia **Dynamic Web Project** (Dynamiczny projekt internetowy) na liście dostępnych projektów po kliknięciu pozycji **File** (Plik) i **New** (Nowy), wykonaj następujące czynności: kliknij pozycję **File** (Plik), kliknij pozycję **New** (Nowy), kliknij pozycję **Project...** (Projekt), rozwiń węzeł **Web** (Internet), kliknij pozycję **Dynamic Web Project** (Dynamiczny projekt internetowy) i kliknij przycisk **Next** (Dalej)).

   ![Tworzenie dynamicznego projektu internetowego][file-new-dynamic-web-project]

2. Na potrzeby tego samouczka nadaj projektowi nazwę **MyWebApp**. Wyświetlony ekran powinien wyglądać podobnie do następującego:
   
   ![Właściwości nowego dynamicznego projektu internetowego][dynamic-web-project-properties]

3. Kliknij przycisk **Zakończ**.

4. W widoku Project Explorer (Eksplorator projektu) w środowisku Eclipse rozwiń węzeł **MyWebApp**. Kliknij prawym przyciskiem myszy folder **WebContent**, kliknij polecenie **New** (Nowy), a następnie kliknij polecenie **JSP File** (Plik JSP).

   ![Tworzenie nowego pliku JSP][create-new-jsp-file]

5. W oknie dialogowym **New JSP File** (Nowy plik JSP) podaj nazwę pliku **index.jsp**, pozostaw folder **MyWebApp/WebContent** jako folder nadrzędny, a następnie kliknij przycisk **Next** (Dalej).

   ![Okno dialogowe New JSP File (Nowy plik JSP)][new-jsp-file-dialog]

6. W oknie dialogowym **Select JSP Template** (Wybieranie szablonu pliku JSP) na potrzeby tego samouczka wybierz szablon **New JSP File (html)** , a następnie kliknij przycisk **Finish** (Zakończ).

   ![Wybieranie szablonu pliku JSP][select-jsp-template]

7. Gdy plik index.jsp zostanie otwarty w środowisku Eclipse, dodaj tekst, aby dynamicznie wyświetlać napis **Hello world!** wewnątrz istniejącego elementu `<body>`. Zaktualizowana zawartość elementu `<body>` powinna przypominać następujący przykład:
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. Zapisz plik index.jsp.

## <a name="deploying-web-app-to-azure"></a>Wdrażanie aplikacji internetowej na platformie Azure

1. W widoku Project Explorer (Eksplorator projektów) środowiska Eclipse kliknij swój projekt prawym przyciskiem myszy, wybierz pozycję **Azure**, a następnie wybierz polecenie **Publish as Azure Web App** (Publikuj jako aplikację internetową platformy Azure).
   
   ![Pozycja Publish as Azure Web App... (Publikuj jako aplikację internetową platformy Azure)][publish-as-azure-web-app]

1. Gdy pojawi się okno dialogowe **Deploy Web App** (Wdrażanie aplikacji internetowej), możesz wybrać jedną z następujących opcji:

   * Wybierz istniejącą aplikację internetową, jeśli istnieje.

      ![Wybieranie usługi App Service][select-app-service]

   * Kliknij przycisk **Create New Web App** (Utwórz nową aplikację internetową).

      ![Tworzenie usługi App Service][create-app-service]

      W oknie dialogowym **Create App Service** (Tworzenie usługi App Service) podaj wymagane informacje dotyczące aplikacji internetowej, a następnie kliknij przycisk **Create** (Utwórz).

      W tym miejscu możesz skonfigurować środowisko uruchomieniowe, ustawienia aplikacji, plan usługi i grupę zasobów.

      ![Okno dialogowe Create App Service (Tworzenie usługi App Service)][create-app-service-dialog]

1. Wybierz aplikację internetową, a następnie kliknij przycisk **Deploy** (Wdróż).

   ![Wdrażanie usługi App Service][deploy-app-service]

1. Po pomyślnym wdrożeniu aplikacji sieci Web zestaw narzędzi będzie wyświetlać stan **Published** (Opublikowano) na karcie **Azure Activity Log** (Dziennik aktywności platformy Azure) po pomyślnym wdrożeniu aplikacji internetowej. Stan tej jest hiperlinkiem adresu URL wdrożonej aplikacji internetowej.

   ![Stan publikowania][publish-status]

1. Aby przejść do aplikacji internetowej, możesz użyć linku dostępnego w komunikacie o stanie.

   ![Przechodzenie do aplikacji internetowej][browse-web-app]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="cleaning-up-resources"></a>Oczyszczanie zasobów

1. Po opublikowaniu aplikacji internetowej na platformie Azure możesz nią zarządzać, klikając prawym przyciskiem myszy w programie Azure Explorer i wybierając jedną z opcji w menu kontekstowym. Na przykład możesz **usunąć** aplikację internetową tutaj, aby wyczyścić zasób związany z tym samouczkiem.

   ![Zarządzanie usługą App Service][manage-app-service]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

Aby uzyskać dodatkowe informacje o tworzeniu funkcji Azure Web Apps, zobacz temat [Web Apps Overview] (Omówienie usługi Web Apps).

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../intellij/azure-toolkit-for-intellij.md
[intellij-hello-world]: ../intellij/azure-toolkit-for-intellij-create-hello-world-web-app.md
[Web Apps Overview]: /azure/app-service/app-service-web-overview (Omówienie usługi Web Apps)
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version.md

<!-- IMG List -->
[I01]: media/azure-toolkit-for-eclipse-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-eclipse-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-eclipse-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-eclipse-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-eclipse-sign-in-instructions/I05.png

[browse-web-app]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/browse-web-app.png
[file-new-dynamic-web-project]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/file-new-dynamic-web-project.png
[dynamic-web-project-properties]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/dynamic-web-project-properties.png
[create-new-jsp-file]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-new-jsp-file.png
[new-jsp-file-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/new-jsp-file-dialog.png
[select-jsp-template]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/select-jsp-template.png
[publish-as-azure-web-app]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/publish-as-azure-web-app.png
[deploy-web-app-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/deploy-web-app-dialog.png
[select-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/select-app-service.png
[create-app-service-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-app-service-dialog.png
[publish-status]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/publish-status.png
[create-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-app-service.png
[deploy-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/deploy-app-service.png
[manage-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/manage-app-service.png
