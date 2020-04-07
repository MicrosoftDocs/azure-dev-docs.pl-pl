---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu środowiska IntelliJ IDEA
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ.
ms.topic: quickstart
ms.date: 03/26/2020
ms.openlocfilehash: 27060e64008507f3f3da7fd3922ddfef4b2ad36e
ms.sourcegitcommit: 3b76a0aa1683f28bcb42cd4d506426b48e5b0397
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537565"
---
# <a name="quickstart-create-an-azure-functions-project-using-intellij-idea"></a>Szybki start: Tworzenie projektu usługi Azure Functions przy użyciu środowiska IntelliJ IDEA

W tym artykule używane jest środowisko IntelliJ IDEA do utworzenia funkcji odpowiadającej na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym usługi Azure Functions. Ukończenie tego przewodnika Szybki start wiąże się z naliczeniem niewielkiej opłaty w wysokości kilku centów USD lub mniej na koncie platformy Azure.

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ [Zestaw Java Development Kit (JDK) obsługiwany przez platformę Azure](https://aka.ms/azure-jdks) dla środowiska Java 8
+ Zainstalowane środowisko [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) w wersji Ultimate Edition lub Community Edition
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Najnowsze narzędzia [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)

## <a name="installation-and-sign-in"></a>Instalacja i logowanie

1. W oknie dialogowym Settings/Preferences (Ustawienia/preferencje) (Ctrl+Alt+S) środowiska IntelliJ IDEA wybierz pozycję **Plugins** (Wtyczki). Następnie znajdź pozycję **Azure Toolkit for IntelliJ** w witrynie **Marketplace** i kliknij pozycję **Install** (Zainstaluj). Po zainstalowaniu kliknij przycisk **Restart** (Uruchom ponownie), aby aktywować wtyczkę. 

   ![Wtyczka Azure Toolkit for IntelliJ w portalu Marketplace][marketplace]

2. Aby zalogować się do konta platformy Azure, otwórz pasek boczny programu **Azure Explorer** i kliknij ikonę **Azure Sign In** (Logowanie do platformy Azure) w górnej części paska (lub z menu IDEA **Tools/Azure/Azure Sign in** [Narzędzia/Azure/Logowanie Azure]).

   ![Polecenie Azure Sign In (Logowanie do platformy Azure) na platformie IntelliJ][I01]

3. W oknie **Azure Sign In** (Logowanie do platformy Azure) wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj się) ([inne opcje logowania](azure-toolkit-for-intellij-sign-in-instructions.md)).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][I02]

4. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij pozycję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe Azure Login (Logowanie Azure)][I03]

5. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania do urządzenia][I04]

6. W oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][I05]

## <a name="create-your-local-project"></a>Tworzenie projektu lokalnego

W tej sekcji użyto zestawu narzędzi Azure Toolkit for IntelliJ do utworzenia lokalnego projektu usługi Azure Functions. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Otwórz okno dialogowe Zapraszamy w środowisku IntelliJ, wybierz pozycję *Create New Project* (Utwórz nowy projekt), aby otworzyć kreatora nowego projektu, a następnie wybierz pozycję *Azure Functions*.

    ![Tworzenie projektu usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/create-functions-project.png)

1. Wybierz pozycję *Http Trigger* (Wyzwalacz http), a następnie kliknij pozycję *Next* (Dalej) i postępuj zgodnie z poleceniami kreatora dotyczącymi konfiguracji na następnych stronach; potwierdź lokalizację projektu, a następnie kliknij pozycję *Finish* (Zakończ); program Intellij IDEA otworzy wtedy Twój nowy projekt.

    ![Kończenie tworzenia projektu usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Uruchamianie aplikacji funkcji lokalnie

1. Przejdź do pliku `src/main/java/org/example/functions/Function.java`, aby wyświetlić wygenerowany kod. Obok wiersza *17* znajduje się zielony przycisk *Run* (Uruchom); kliknij go i wybierz pozycję *Run 'azure-function-exam...'* (Uruchom polecenie „azure-function-exam...”). Aplikacja funkcji zostanie uruchomiona lokalnie z kilkoma dziennikami.

    ![Uruchomienie lokalne — projekt usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/local-run-functions-project.png)

    ![Uruchomienie lokalne — dane wyjściowe usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/local-run-functions-output.png)

1. Możesz wypróbować funkcję, uzyskując dostęp do wyświetlonego punktu końcowego z poziomu przeglądarki, np. `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Uruchomienie lokalne — wynik testu usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/local-run-functions-test.png)

1. Dziennik jest również wyświetlany w Twoim środowisku IDEA. Teraz zatrzymaj funkcję, klikając przycisk *Stop* (Zatrzymaj).

    ![Uruchomienie lokalne — dziennik testu usługi Functions](media/azure-toolkit-for-intellij-quickstart-functions/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Lokalne debugowanie aplikacji usługi funkcji

1. Teraz spróbujmy zdebugować aplikację funkcji lokalnie. Kliknij przycisk *Debug* (Debuguj) na pasku narzędzi (jeśli go nie widzisz, kliknij pozycję *View -> Appearance -> Toolbar* (Widok -> Wygląd -> Pasek narzędzi), aby włączyć pasek narzędzi).

    ![Przycisk lokalnego debugowania funkcji](media/azure-toolkit-for-intellij-quickstart-functions/local-debug-functions-button.png)

1. Kliknij wiersz *20* w pliku `src/main/java/org/example/functions/Function.java`, aby dodać punkt przerwania. Ponownie uzyskaj dostęp do punktu końcowego `http://localhost:7071/api/HttpTrigger-Java?name=Azure`, a zauważysz, że punkt przerwania został trafiony. Możesz wypróbować więcej funkcji debugowania takich jak *krok*, *wyrażenie kontrolne* czy *ocena*. Zatrzymaj sesję debugowania, klikając przycisk Stop (Zatrzymaj).

    ![Przerwanie lokalnego debugowania funkcji](media/azure-toolkit-for-intellij-quickstart-functions/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Wdrażanie aplikacja funkcji na platformie Azure

1. Kliknij prawym przyciskiem myszy swój projekt w Eksploratorze projektów Intellij i wybierz pozycję *Azure -> Deploy to Azure Functions* (Azure -> Wdróż do usługi Azure Functions)

    ![Wdrażanie funkcji na platformie Azure](media/azure-toolkit-for-intellij-quickstart-functions/deploy-functions-to-azure.png)

1. Jeśli nie masz jeszcze żadnej aplikacji funkcji, kliknij pozycję *No available function, click to create a new one* (Brak dostępnej funkcji, kliknij, aby utworzyć nową).

    ![Wdrażanie funkcji na platformie Azure — tworzenie aplikacji](media/azure-toolkit-for-intellij-quickstart-functions/deploy-functions-create-app.png)

1. Wpisz nazwę aplikacji funkcji i wybierz odpowiednią subskrypcję / platformę / grupę zasobów/ plan usługi App Service. W tym miejscu możesz także utworzyć grupę zasobów / plan usługi App Service. Następnie pozostaw ustawienia aplikacji bez zmian, kliknij przycisk *OK* i poczekaj kilka minut na utworzenie nowej funkcji. Po zakończeniu pasek postępu *Creating New Function App...* (Tworzenie nowej aplikacji funkcji...) zniknie.

    ![Wdrażanie funkcji na platformie Azure — kreator tworzenia aplikacji](media/azure-toolkit-for-intellij-quickstart-functions/deploy-functions-create-app-wizard.png)

1. Wybierz aplikację funkcji, którą chcesz wdrożyć (nowo utworzona aplikacja funkcji zostanie automatycznie zaznaczona). Kliknij pozycję *Run* (Uruchom), aby wdrożyć funkcje.

    ![Wdrażanie funkcji na platformie Azure — uruchamianie](media/azure-toolkit-for-intellij-quickstart-functions/deploy-functions-run.png)

    ![Wdrażanie funkcji na platformie Azure — dziennik](media/azure-toolkit-for-intellij-quickstart-functions/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Zarządzanie usługą Azure Functions ze środowiska IDEA

1. Możesz zarządzać swoimi funkcjami przy użyciu *Eksploratora platformy Azure* w środowisku IDEA. Kliknij pozycję *Function App* (Aplikacja funkcji), a zostaną wyświetlona wszystkie Twoje funkcje.

    ![Wyświetlanie funkcji w eksploratorze](media/azure-toolkit-for-intellij-quickstart-functions/explorer-view-functions.png)

1. Kliknij, aby wybrać jedną z funkcji, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję *Show Properties* (Pokaż właściwości), aby otworzyć stronę ze szczegółami. 

    ![Wyświetlanie właściwości funkcji](media/azure-toolkit-for-intellij-quickstart-functions/explorer-functions-show-properties.png)

1. Kliknij prawym przyciskiem myszy funkcję *HttpTrigger-Java* i wybierz pozycję *Trigger Function* (Wyzwól funkcję). Zostanie wtedy otwarta przeglądarka z adresem URL wyzwalacza.

    ![Wdrażanie funkcji na platformie Azure — uruchamianie](media/azure-toolkit-for-intellij-quickstart-functions/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Dodawanie większej liczby funkcji do projektu

1. Kliknij prawym przyciskiem myszy pakiet *org.example.functions* i wybierz pozycję *New -> Azure Function Class* (Nowe -> Klasa funkcji platformy Azure). 

    ![Dodawanie większej liczby funkcji do projektu — wpis](media/azure-toolkit-for-intellij-quickstart-functions/add-functions-entry.png)

1. Wpisz nazwę klasy *HttpTest*, wybierz pozycję *HttpTrigger* w kreatorze tworzenia klasy funkcji i kliknij przycisk *OK*. W ten sposób możesz utworzyć dowolną liczbę nowych funkcji.

    ![Dodawanie większej liczby funkcji do projektu — wybieranie wyzwalacza](media/azure-toolkit-for-intellij-quickstart-functions/add-functions-trigger.png)
    
    ![Dodawanie większej liczby funkcji do projektu — dane wyjściowe](media/azure-toolkit-for-intellij-quickstart-functions/add-functions-output.png)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Java z funkcją wyzwalaną przez protokół HTTP, uruchomiono go na komputerze lokalnym i wdrożono na platformie Azure. Teraz możesz rozszerzyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodanie powiązania danych wyjściowych kolejki usługi Azure Storage](https://docs.microsoft.com/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/azure-toolkit-for-intellij-create-hello-world-web-app/marketplace.png
[I01]: media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-intellij-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-intellij-sign-in-instructions/I05.png
