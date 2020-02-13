---
title: Przeglądanie danych przy użyciu narzędzi Java Flight Recorder i Mission Control
description: Wskazówki dotyczące gromadzenia danych aplikacji i przeglądania ich przy użyciu narzędzi Java Flight Recorder i Mission Control.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 14084d2fb89c8adc78a0455b8bdd2cc0b5b6ec4c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999935"
---
# <a name="monitor-and-manage-java-workloads-with-java-flight-recorder-jfr-and-zulu-mission-control"></a>Monitorowanie obciążeń Java i zarządzanie nimi przy użyciu narzędzi Java Flight Recorder (JFR) i Zulu Mission Control

W tych artykułach przedstawiono sposób monitorowania obciążeń Java i zarządzanie nimi przy użyciu narzędzi Java Flight Recorder (JFR) i Zulu Mission Control.

Zulu Mission Control to w pełni przetestowana kompilacja zestawu JDK Mission Control, który został udostępniony jako oprogramowanie typu „open source” przez firmę Oracle w 2018 r. i jest zarządzany jako projekt w ramach projektu nadrzędnego OpenJDK. W połączeniu z narzędziem Flight Recorder narzędzie Mission Control zapewnia możliwości interaktywnego monitorowania obciążeń Java i zarządzania nimi o niskim obciążeniu.

Narzędzie Zulu Mission Control jest zgodne z następującymi zestawami JDK/środowiskami JRE:

* Zulu 12.1 i nowsze
* Zulu 11.0 i nowsze
* Zulu 8u202 (8.36) i nowsze
* Oracle OpenJDK 11+15 i nowsze
* Oracle Java 11.0 i nowsze
* Oracle Java 8.0 i nowsze

Wykonaj poniższe kroki, aby zainstalować narzędzie Zulu Mission Control, nawiązać połączenie z wirtualną maszyną Java (JVM) i uzyskać wgląd w czasie rzeczywistym we wszystkie aspekty działającej aplikacji.

1.  [Zainstaluj zestaw JDK/środowisko JRE zgodne z narzędziem Zulu Mission Control](java-jdk-install.md).

2.  Pobierz narzędzie Zulu Mission Control z [witryny pobierania Azul](https://www.azul.com/products/zulu-mission-control/), wybierz odpowiednią wersję dla systemu, zapisz je lokalnie i przejdź do tego katalogu.

3.  Rozwiń pobrany plik.

    **Linux:**

    ```cli
    tar -xzvf zmc7.0.0-EA-linux_x64.tar.gz
    ```

    **Windows:**

    ```cli
    unzip -zxvf zmc7.0.0-EA-win_x64.zip 
    ```

    **MacOS:**

    ```cli
    tar -xzvf zmc7.0.0-EA-macosx_x64.tar.gz
    ```

4.  Uruchom aplikację Java przy użyciu jednego ze zgodnych zestawów JDK. Na przykład:

    ```cli
    $JAVA_HOME/bin/java -jar MyApplication.jar
    ```

5.  Uruchamianie narzędzia Zulu Mission Control

    **Linux:**

    ```cli
    zmc7.0.0-EA-linux_x64/zmc
    ```

    **Windows:**

    ```cli
    zmc7.0.0-EA-win_x64\zmc.exe 
    ```

    **MacOS:**

    ```cli
    zmc7.0.0-EA-macosx_x64/Zulu\ Mission\ Control.app/Contents/MacOS/zmc
    ```

6.  Przełączanie instalacji wirtualnej maszyny Java (JVM) dla narzędzia Mission Control (opcjonalnie)

    W systemie Windows plik *zmc.exe* będzie używać domyślnej instalacji wirtualnej maszyny Java (JVM) skonfigurowanej w rejestrze. Narzędzie Zulu Mission Control należy uruchomić z poziomu pełnego zestawu JDK, aby lokalne wystąpienia wirtualnej maszyny Java (JVM) mogły być automatycznie wykrywane. Jeśli jest to środowisko JRE, zostanie wyświetlone poniższe ostrzeżenie:

    > [!div class="mx-imgBorder"]
    ![Ostrzeżenie, jeśli instalacja zestawu JDK jest przeznaczona tylko dla środowiska JRE](../media/jdk/jfr-jre-warning-message.png)

    Aby zmienić wirtualną maszynę Java (JVM) używaną przez narzędzie Mission Control, wykonaj następujące kroki: 
    1.  Otwórz plik konfiguracji *zmc.ini* znajdujący się w tym samym katalogu, co plik *zmc.exe*
    2.  Przed wierszem `-vmargs` dodaj dwa wiersze:
        * W pierwszym wierszu wpisz `–vm`
        * W drugim wierszu wpisz ścieżkę do instalacji zestawu JDK. (Na przykład: `C:\Program Files\Java\jdk1.8.0_212\bin\javaw.exe`).

7.  Lokalizowanie wirtualnej maszyny Java (JVM), na której uruchomiona jest aplikacja
    1.  W lewym górnym okienku w oknie narzędzia Zulu Mission Control wybierz kartę z etykietą **JVM Browser** (Przeglądarka JVM).
    2.  Wybierz i rozwinąć element listy w lewym górnym rogu dla wystąpienia wirtualnej maszyny Java (JVM), na którym jest uruchomiona aplikacja.

    > [!div class="mx-imgBorder"]
    ![Rozwiń element listy w lewym górnym rogu wystąpienia wirtualnej maszyny Java (JVM)](../media/jdk/jfr-jvm-instance-dashboard.png)


8.  W razie potrzeby rozpocznij rejestrowanie przy użyciu narzędzia Flight Recorder
    1.  Jeśli w narzędziu Flight Recorder jest wyświetlany komunikat „No Recordings” (Brak nagrań), uruchom nagrywanie, klikając prawym przyciskiem myszy w wierszu narzędzia Flight Recorder na karcie JVM Browser (Przeglądarka JVM) i wybierając pozycję **Start Flight Recording** (Rozpocznij nagrywanie przy użyciu narzędzia Flight Recorder).
    2.  Wybierz nagrywanie przez ustalony czas lub ciągłe nagrywanie oraz konfigurację Profilowanie (szczegółową) lub konfigurację Ciągła (mniejsze obciążenie), a następnie wybierz pozycję **Zakończ**.

    > [!div class="mx-imgBorder"]
    ![Rozpoczynanie nagrywania przy użyciu narzędzia Flight Recorder](../media/jdk/jfr-start-flight-recording.png)

9.  Zrzucanie nagrania narzędzia Flight Recorder
    1.  Nagranie narzędzia Flight Recorder powinno być wyświetlane poniżej wiersza narzędzia Flight Recorder na karcie JVM Browser (Przeglądarka JVM). Kliknij prawym przyciskiem myszy wiersz reprezentujący nagranie narzędzia Flight Recorder i wybierz pozycję **Dump whole recording** (Zrzuć całe nagranie).
    2.  Nowa karta zostanie wyświetlona w dużym okienku po prawej stronie okna narzędzia Zulu Mission Control. To okienko reprezentuje nagranie narzędzia Flight Recorder z wirtualnej maszyny Java (JVM), na której uruchomiona jest aplikacja.

10. Analizowanie nagrania narzędzia Flight Recorder przy użyciu narzędzia Zulu Mission Control
    1.  Jeśli nie została jeszcze aktywowana, wybierz kartę z etykietą **Outline** (Konspekt) w lewym okienku okna narzędzia Zulu Mission Control. Ta karta zawiera różne widoki danych zebranych w nagraniu narzędzia Flight Recorder.
 
    > [!div class="mx-imgBorder"]
    ![Przeglądanie nagrania narzędzia Flight Recorder](../media/jdk/jfr-zulu-mission-control-data.png)

## <a name="resources"></a>Zasoby

Przygotowano również [film pokazowy](https://www.azul.com/presentation/azul-webinar-open-source-flight-recorder-and-mission-control-managing-and-measuring-openjdk-8-performance/), którego narratorem jest Simon Ritter, zastępca dyrektora ds. technicznych firmy Azul Systems. Ten film przeprowadzi Cię przez proces konfigurowania narzędzi Flight Recorder i Zulu Mission Control. Omówienie narzędzia Flight Recorder zaczyna się od 31:30.

