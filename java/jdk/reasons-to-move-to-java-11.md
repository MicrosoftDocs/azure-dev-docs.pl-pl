---
title: Powody do migracji do środowiska Java 11
description: Dokument podsumowujący przeznaczony dla osób podejmujących decyzje, które rozważają korzyści przejścia ze środowiska Java 8 do środowiska Java 11.
author: dsgrieve
manager: maverberg
tags: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: 7daf058c2abebbf2cca85dadc4f9ffe3e8771fa1
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999942"
---
# <a name="reasons-to-move-to-java-11"></a>Powody do migracji do środowiska Java 11

Pytanie nie brzmi *„czy”* należy przejść do środowiska Java 11, ale *„kiedy”* . W ciągu następnych kilku lat środowisko Java 8 nie będzie już wspierane i użytkownicy będą musieli przejść do środowiska Java 11. Firma Microsoft uważa, że przejście do środowiska Java 11 przynosi wiele korzyści i zachęca zespoły zrobienia tego najszybciej, jak to możliwe.

Dodano nowe funkcje i wprowadzono ulepszenia względem środowiska Java 8. Dodano składniki i wprowadzono zauważalne modyfikacje interfejsu API orz usprawnienia, które zwiększają skracają czas uruchamiania, zwiększają wydajność i poprawiają wykorzystanie pamięci.

## <a name="transitioning-to-java-11"></a>Przejście do środowiska Java 11

Przejście do środowiska Java 11 można wykonać stopniowo. Aby kod działał on w środowisku Java 11, *nie* jest wymagane stosowanie modułów Java. Środowisko Java 11 może służyć do uruchamiania kodu opracowanego i utworzonego za pomocą zestawu JDK 8.
Jednak istnieją potencjalne problemy, dotyczące głównie przestarzałego interfejsu API, modułów ładujących klasy i odbicia.

Kompleksowy przewodnik przejścia ze środowiska Java 8 do środowiska Java 11 zostanie w przyszłości udostępniony przez grupę inżynierów Java firmy Microsoft. Tymczasem dostępnych jest wiele przewodników dotyczących przejścia ze środowiska Java 8 do środowiska Java 9, które ułatwią rozpoczęcie pracy. Na przykład [Java Platform, Standard Edition Oracle JDK 9 Migration Guide](https://docs.oracle.com/javase/9/migrate/toc.htm) (Pla Java, Oracle JDK 9 Standard Edition — Przewodnik migracji) i [The State of the Module System: Compatibility and Migration](http://openjdk.java.net/projects/jigsaw/spec/sotms/#compatibility--migration) (Stan systemu modułów: zgodność i migracja).

## <a name="high-level-changes-between-java-8-and-11"></a>Zmiany wysokiego poziomu w środowisku Java 11 względem środowiska Java 8

W tej sekcji nie przedstawiono listy wszystkich zmian dokonanych w środowisku Java w wersjach 9 \[[1](#ref1)\], 10 \[[2](#ref2)\] i 11 \[[3](#ref3)\]. Wyróżniono zmiany, które mają wpływ na wydajność, diagnostykę i produktywność.

### <a name="modules-4ref4"></a>Moduły \[[4](#ref4)\]

Moduły rozwiązują problemy dotyczące konfiguracji i hermetyzacji, którymi trudno zarządzać w aplikacjach o dużej skali uruchomionych na *ścieżce klasy*. *Moduł* to samoopisująca kolekcja klas i interfejsów języka Java oraz powiązanych zasobów.

Moduły umożliwiają dostosowywanie konfiguracji środowiska uruchomieniowego, które zawierają tylko składniki wymagane przez aplikację. To dostosowanie powoduje mniejsze zużycie pamięci i umożliwia statyczne łączenie aplikacji przy użyciu narzędzia [jlink](https://docs.oracle.com/en/java/javase/11/tools/jlink.html) w niestandardowe środowisko uruchomieniowe na potrzeby wdrożenia. Mniejsze zużycie pamięci może być szczególnie przydatne w architekturze mikrousług.

Wewnętrznie wirtualna maszyna Java (JVM) jest w stanie korzystać z modułów w taki sposób, aby zwiększyć efektywność ładowania klas. W efekcie pozwala to uzyskać środowisko uruchomieniowe, które jest mniejsze, lżejsze i umożliwia szybsze uruchamianie. Techniki optymalizacji używane przez JVM w celu zwiększenia wydajności aplikacji mogą być bardziej efektywne, ponieważ moduły kodują składniki, których wymaga klasa.

Dla programistów moduły mogą wymuszać silną hermetyzację, wymagając jawnej deklaracji, które pakiety eksportuje moduł i jakich składników wymaga, oraz ograniczając dostęp odbijający.
Ten poziom hermetyzacji sprawia, że aplikacja jest bezpieczniejsza i łatwiejsza w obsłudze.

Aplikacja może nadal używać *ścieżki klas*, a przejście na moduły nie jest wymagane do uruchamiania w środowisku Java 11.

### <a name="profiling-and-diagnostics"></a>Profilowanie i diagnostyka

#### <a name="java-flight-recorder-5ref5"></a>Java Flight Recorder \[[5](#ref5)\]

Narzędzie Java Flight Recorder (JFR) zbiera dane diagnostyczne i profilowania z uruchomionej aplikacji Java. JFR ma niewielki wpływ na działającą aplikację Java. Zebrane dane można następnie analizować przy użyciu narzędzia Java Mission Control (JMC) i innych narzędzi. Narzędzia JFR i JMC były funkcjami komercyjnymi w środowisku Java 8, jednak oba te narzędzia stanowią oprogramowanie open source w środowisku Java 11.

#### <a name="java-mission-control-6ref6"></a>Java Mission Control \[[6](#ref6)\]

Narzędzie Java Mission Control (JMC) zapewnia graficzne wyświetlanie danych zebranych przez narzędzie Java Flight Recorder (JFR) i jest oprogramowaniem open source w języku Java.
11. Oprócz ogólnych informacji o działającej aplikacji JMC umożliwia użytkownikowi przechodzenie do szczegółów danych. JFR i JMC mogą służyć do diagnozowania problemów dotyczących środowiska uruchomieniowego, takich jak przecieki pamięci, obciążenie GC, metody gorące, wąskie gardła wątków i blokowanie operacji we/wy.

#### <a name="unified-logging-7ref7"></a>Ujednolicone rejestrowanie \[[7](#ref7)\]

Środowisko Java 11 ma wspólny system rejestrowania dla wszystkich składników JVM.
Ten ujednolicony system rejestrowania umożliwia użytkownikowi definiowanie składników do rejestrowania oraz jego poziomu. To szczegółowe rejestrowanie jest przydatne do wykonywania analiz głównych przyczyn w przypadku awarii JVM oraz do diagnozowania problemów z wydajnością w środowisku produkcyjnym.

#### <a name="low-overhead-heap-profiling-8ref8"></a>Profilowanie sterty z niskim obciążeniem \[[8](#ref8)\]

Dodano nowy interfejs API do interfejsu JVMTI (Java Virtual Machine Tool Interface) służący do próbkowania alokacji sterty Java. Próbkowanie powoduje niewielkie obciążenie i może być stale włączone. Alokacja sterty może być monitorowana przy użyciu narzędzia Java Flight Recorder (JFR), metoda próbkowania w JFR działa tylko w przypadku alokacji. Implementacja JFR może również pomijać alokacje. Natomiast próbkowanie sterty w środowisku Java 11 umożliwia uzyskiwanie informacji o obiektach aktywnych i nieaktywnych.

Dostawcy rozwiązań monitorowania wydajności aplikacji (APM) zaczynają korzystać z tej nowej funkcji, a grupa inżynierów Java bada jej potencjalne możliwości jej wykorzystania za pomocą narzędzi do monitorowania wydajności platformy Azure.

#### <a name="stackwalker-9ref9"></a>StackWalker \[[9](#ref9)\]

Pobieranie migawki stosu dla bieżącego wątku jest często używane podczas rejestrowania. Problem polega na tym, jaki zakres śladu stosu ma być rejestrowany, oraz czy w ogóle należy rejestrować ślad stosu. Na przykład jedna osoba może chcieć zobaczyć ślad stosu tylko dla pewnego wyjątku z metody. Klasa StackWalker (dodana w języku Java 9) zawiera migawkę stosu i udostępnia metody, które umożliwiają programistom precyzyjne sterowanie sposobem korzystania ze śladu stosu.

### <a name="garbage-collection-10ref10"></a>Odzyskiwanie pamięci \[[10](#ref10)\]

W środowisku Java 11 dostępne są następujące moduły odzyskiwania pamięci: Serial, Parallel, Garbage-First i Epsilon. Domyślny moduł zbierający elementy bezużyteczne w środowisku Java 11 to Garbage First Garbage Collector (G1GC).

Pozostałe moduły zbierające wymieniono w celu zapewnienia kompletnych informacji. Z Garbage Collector (ZGC) do współbieżny moduł zbierający o niskim opóźnieniu, który próbuje utrzymać czas wstrzymania na poziomie poniżej 10 ms. Moduł ZGC jest dostępny jako funkcja eksperymentalna w środowisku Java 11. Moduł zbierający Shenandoah jest modułem zbierającym o krótkim czasie wstrzymania, który skraca czas wstrzymania odzyskiwania pamięci przez wykonywanie odzyskiwania pamięci w większym zakresie współbieżnie z uruchomionym programem Java.
Shenandoah to eksperymentalna funkcja w środowisku Java 12, ale istnieją wersje dostosowane do środowiska Java 11. Moduł zbierający Concurrent Mark and Sweep (CMS) jest dostępny, ale został uznany za przestarzały od wersji 9 środowiska Java.

JVM ustawia wartości domyślne odzyskiwania pamięci dla typowego przypadku użycia. Często te wartości domyślne i inne ustawienia odzyskiwania pamięci muszą zostać dostosowane w celu uzyskania optymalnej przepływności lub opóźnienia, zgodnie z wymaganiami aplikacji.
Odpowiednie dostosowywanie odzyskiwania pamięci wymaga dogłębnej znajomości tego tematu, którą cechuje się [grupa inżynierów Java firmy Microsoft](mailto:javaplatformgroup@microsoft.com).

#### <a name="g1gc"></a>G1GC

Domyślny moduł zbierający elementy bezużyteczne w środowisku Java 11 to moduł zbierający G1 (G1GC). Celem G1GC jest uzyskanie równowagi między opóźnieniami a przepływnością. Moduł zbierający G1 próbuje uzyskać wysoką przepływność dzięki dużemu prawdopodobieństwu realizacji celów dotyczących czasu wstrzymania. G1GC jest zaprojektowany tak, aby unikać pełnego zbierania, ale gdy współbieżne procesy zbierania nie mogą odzyskiwać pamięci wystarczająco szybko, zostanie wykonane rezerwowe pełne odzyskiwanie pamięci. Pełne odzyskiwanie pamięci wykorzystuje taką samą liczbę równoległych wątków roboczych, jak w przypadku zbierania elementów młodych i mieszanych.

#### <a name="parallel-gc"></a>Parallel GC

Równoległy moduł zbierający jest domyślnym modułem zbierającym w środowisku Java 8. Równoległy moduł zbierający jest modułem zbierającym przepływności, który korzysta z wielu wątków w celu przyspieszenia odzyskiwania pamięci.

#### <a name="epsilon-11ref11"></a>Epsilon \[[11](#ref11)\]

Moduł zbierający Epsilon obsługuje alokacje, ale nie odzyskuje pamięci. Po wyczerpaniu sterty wirtualna maszyna Java (JVM) zostanie zamknięta.
Moduł Epsilon jest przydatny w przypadku krótkoterminowych usług i aplikacji, które są wolne od elementów bezużytecznych.

#### <a name="improvements-for-docker-containers-12ref12"></a>Ulepszenia dotyczące kontenerów platformy Docker \[[12](#ref12)\]

W środowiskach starszych niż Java 10 ograniczenia dotyczące pamięci i procesora CPU ustawione w kontenerze nie były rozpoznawane przez JVM. Na przykład w środowisku Java 8 JVM domyśnie ustawia maksymalny rozmiar sterty na ¼ pamięci fizycznej podstawowego hosta. Począwszy od środowiska Java 10, JVM używa ograniczeń ustawionych przez grupy kontroli kontenerów (cgroup) do ustawiania limitów pamięci i procesora (patrz uwaga poniżej).
Na przykład domyślny maksymalny rozmiar sterty to ¼ limitu pamięci kontenera (np. 500 MB dla -m2G).

Dodano również opcje JVM, aby umożliwić użytkownikom kontenera platformy Docker precyzyjne sterowanie ilością pamięci systemowej, która będzie używana dla sterty języka Java.

Ta obsługa jest domyślnie włączona i jest dostępna tylko na platformach opartych na systemie Linux.

> [!NOTE]
> Większość zmian związanych z obsługą cgroup została wdrożona w środowisku Java 8 od wersji jdk8u191. Dalsze ulepszenia niekoniecznie zostaną dostosowane do wersji 8.

#### <a name="multi-release-jar-files-13ref13"></a>Pliki jar zawierające wiele wersji \[[13](#ref13)\]

W środowisku Java 11 można utworzyć plik JAR, który zawiera wiele wersji plików klas przeznaczonych dla określonych wydań środowiska Java. Pliki jar z wieloma wersjami umożliwiają deweloperom bibliotek obsługę wielu wersji środowiska Java bez konieczności dostarczania wielu wersji plików jar. W przypadku użytkowników tych bibliotek pliki jar zawierające różne wersje rozwiązują problem z koniecznością dopasowania określonych plików jar do określonych elementów docelowych środowiska uruchomieniowego.

## <a name="miscellaneous-performance-improvements"></a>Różne ulepszenia wydajności

Następujące zmiany w JVM mają bezpośredni wpływ na wydajność.

-   **JEP 197: Segmentowana pamięć podręczna kodu** \[[14](#ref14)\] — Dzieli pamięć podręczną kodu na oddzielne segmenty. Ta segmentacja zapewnia lepszą kontrolę nad pamięcią JVM, skraca czas skanowania skompilowanych metod, znacznie zmniejsza fragmentację pamięci podręcznej kodu i poprawia wydajność.

-   **JEP 254: Kompaktowe ciągi** \[[15](#ref15)\] — Zmienia wewnętrzną reprezentację ciągu z dwóch bajtów na znak na jeden lub dwa bajty na znak, zależnie od kodowania znaków. Ponieważ większość ciągów zawiera znaki ISO-8859-1/Latin-1, ta zmiana skutecznie zmniejsza o połowę ilość miejsca wymaganego do przechowywania ciągu.

-   **JEP 310: Udostępnianie danych klas aplikacji** \[[16](#ref16)\] — Udostępnianie danych klas skraca czas uruchamiania, umożliwiając mapowanie pamięci dla klas podczas wykonywania. Udostępnianie danych klas aplikacji rozszerza udostępnianie danych klas, umożliwiając umieszczanie klas aplikacji w archiwum CDS. Gdy wiele JVM współdzieli ten sam plik archiwum, pamięć jest zapisywana i łączny czas odpowiedzi systemu jest krótszy.

-   **JEP 312: Uzgodnienia lokalne wątków** \[[17](#ref17)\] — Umożliwiają wykonywanie wywołań zwrotnych w wątkach bez tworzenia globalnego punktu odzyskiwania maszyny wirtualnej, co pomaga w zmniejszeniu opóźnienia maszyny wirtualnej przez ograniczenie liczby globalnych punktów odzyskiwania.

-   **Leniwe przydzielanie wątków kompilatora** \[[18](#ref18)\] — W trybie kompilacji warstwowej maszyna wirtualna uruchamia dużą liczbę wątków kompilatora.
    Jest to tryb domyślny w systemach z wieloma procesorami. Te wątki są tworzone bez względu na dostępną pamięć lub liczbę żądań kompilacji. Wątki zużywają pamięć nawet wtedy, gdy są bezczynne (niemal przez cały czas), co prowadzi do niewydajnego korzystania z zasobów. Aby rozwiązać ten problem, implementacja została zmieniona tak, aby uruchamiała tylko jeden wątek kompilatora dla każdego typu podczas uruchamiania. Uruchamianie dodatkowych wątków i zamykanie nieużywanych wątków jest obsługiwane dynamicznie. 

Następujące zmiany w bibliotekach podstawowych mają wpływ na wydajność nowego lub zmodyfikowanego kodu.

-   **JEP 193: Dojścia zmiennych** \[[19](#ref19)\] — Definiuje standardową metodę wywołania odpowiedników różnych operacji java.util.concurrent.atomic i sun.misc.Unsafe w przypadku pól obiektów i elementów tablicy, standardowego zestawu operacji ogrodzenia do precyzyjnej kontroli nad kolejnością pamięci oraz standardowej operacji ograniczenia dostępności w celu zapewnienia, że obiekt, do którego istnieje odwołanie, będzie stale osiągalny.

-   **JEP 269: Wygodne metody tworzące dla kolekcji** \[[20](#ref20)\] — Definiuje interfejsy API biblioteki, umożliwiając wygodne tworzenie wystąpień kolekcji i mapowanie przy niewielkiej liczbie elementów. Statyczne metody tworzące w interfejsach kolekcji, które tworzą kompaktowe, niemodyfikowalne wystąpienia kolekcji. Te wystąpienia są z natury bardziej wydajne. Interfejsy API tworzą kolekcje, które są kompaktowo reprezentowane i nie mają klasy otoki.

-   **JEP 285: Wskazówki dotyczące oczekiwania z powodu pętli** \[[21](#ref21)\] — Zapewnia interfejs API, który umożliwia językowi Java informowanie systemu czasu wykonywania o wystąpieniu pętli. Na niektórych platformach sprzętowych korzystne jest wskazanie programowe, że wątek jest w stanie zajętości.

-   **JEP 321: Klient HTTP (wersja standardowa)** \[[22](#ref22)\]— Udostępnia nowy interfejs API klienta HTTP, który implementuje protokół HTTP/2 i technologię WebSocket, i może zastąpić starszy interfejs API HttpURLConnection.

## <a name="references"></a>Dokumentacja

<a id="ref1">\[1\]</a> Oracle Corporation, \"Java Development Kit 9 — informacje o wersji,\" (online). Dostępne: https://www.oracle.com/technetwork/java/javase/9u-relnotes-3704429.html.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref2">\[2\]</a> Oracle Corporation, \"Java Development Kit 10 — informacje o wersji ,\" (online). Dostępne: https://www.oracle.com/technetwork/java/javase/10u-relnotes-4108739.html.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref3">\[3\]</a> Oracle Corporation, \"Java Development Kit 11 — informacje o wersji,\" (online). Dostępne: https://www.oracle.com/technetwork/java/javase/11u-relnotes-5093844.html.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref4">\[4\]</a> Oracle Corporation, \"Projekt Jigsaw,\" 22 września, 
2017. (online). Dostępne: http://openjdk.java.net/projects/jigsaw/.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref5">\[5\]</a> Oracle Corporation, \"JEP 328: Flight Recorder,\" 9 września 2018 r. (online). Dostępne: http://openjdk.java.net/jeps/328. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref6">\[6\]</a> Oracle Corporation, \"Mission Control,\" 25 kwietnia 2019 r. (online). Dostępne: https://wiki.openjdk.java.net/display/jmc/Main. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref7">\[7\]</a> Oracle Corporation, \"JEP 158: Ujednolicone rejestrowanie JVM,\" 14 lutego 2019 r. (online). Dostępne: http://openjdk.java.net/jeps/158.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref8">\[8\]</a> Oracle Corporation, \"JEP 331: Profilowanie sterty z niskim obciążeniem,\" 5 września 2018 r. (online). Dostępne: http://openjdk.java.net/jeps/331. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref9">\[9\]</a> Oracle Corporation, \"JEP 259: Interfejs API przechodzenia przez stos,\" 18 lipca 2017 r. (online).
Dostępne: http://openjdk.java.net/jeps/259. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref10">\[10\]</a> Oracle Corporation, \"JEP 248: Ustaw G1 jako domyślny moduł zbierający,\" 12 września 2017 r. (online). Dostępne: http://openjdk.java.net/jeps/248. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref11">\[11\]</a> Oracle Corporation, \"JEP 318: Epsilon: Moduł zbierający niewykonujący żadnych operacji,\" 24 września 2018 r.
(online). Dostępne: http://openjdk.java.net/jeps/318. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref12">\[12\]</a> Oracle Corporation, \"JDK-8146115: Popraw wykrywanie kontenerów platformy Docker i wykorzystanie konfiguracji zasobów,\" 16 września 2019 r.
(online). Dostępne: https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=JDK-8146115.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref13">\[13\]</a> Oracle Corporation, \"JEP 238: Pliki JAR zawierające wiele wersji,\" 22 czerwca 2017 r. (online). Dostępne: http://openjdk.java.net/jeps/238. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref14">\[14\]</a> Oracle Corporation, \"JEP 197: Segmentowana pamięć podręczna kodu,\" 28 kwietnia 2017 r. (online).
Dostępne: http://openjdk.java.net/jeps/197. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref15">\[15\]</a> Oracle Corporation, \"JEP 254: Kompaktowe ciągi,\" 18 maja 2019 r. (online). Dostępne: http://openjdk.java.net/jeps/254.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref16">\[16\]</a> Oracle Corporation, \"JEP 310: Udostępnianie danych klasy aplikacji,\" 17 sierpnia 2018 r. (online). Dostępne: https://openjdk.java.net/jeps/310. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref17">\[17\]</a> Oracle Corporation, \"JEP 312: Uzgodnienia lokalne wątków,\" 21 sierpnia 2019 r.
(online). Dostępne: https://openjdk.java.net/jeps/312. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref18">\[18\]</a> Oracle Corporation, \"JDK-8198756: Leniwe przydzielanie wątków kompilatora,\" 29 października 2018 r. (online). Dostępne: https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=8198756.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref19">\[19\]</a> Oracle Corporation, \"JEP 193: Dojścia zmiennych,\" 17 sierpnia 2017 r. (online). Dostępne: https://openjdk.java.net/jeps/193. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref20">\[20\]</a> Oracle Corporation, \"JEP 269: Wygodne metody tworzące dla kolekcji,\" 26 czerwca 2017 r. (online). Dostępne: https://openjdk.java.net/jeps/269.
(Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref21">\[21\]</a> Oracle Corporation, \"JEP 285: Wskazówki dotyczące oczekiwania z powodu pętli,\" 20 sierpnia 2017 r. (online). Dostępne: https://openjdk.java.net/jeps/285. (Uzyskano dostęp 13 listopada 2019 r.).

<a id="ref22">\[22\]</a> Oracle Corporation, \"JEP 321: Klient HTTP (wersja standardowa),\" 27 września 2018 r. (online).
Dostępne: https://openjdk.java.net/jeps/321. (Uzyskano dostęp 13 listopada 2019 r.).
