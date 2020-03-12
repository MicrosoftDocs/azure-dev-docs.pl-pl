---
title: Przejście ze środowiska Java 8 do środowiska Java 11
titleSuffix: Azure
description: Przewodnik po procesie przechodzenia ze środowiska Java 8 do środowiska Java 11.
author: dsgrieve
manager: maverbur
tags: java
ms.service: azure
ms.devlang: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: 528b111e945bb68bd18c849847522a070259c0f3
ms.sourcegitcommit: f1e3c72c38376b15f5313d4bfe5fefdbfc022dc9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79022298"
---
# <a name="transition-from-java-8-to-java-11"></a>Przejście ze środowiska Java 8 do środowiska Java 11

Nie ma jednego, uniwersalnego sposobu na przeniesienie kodu ze środowiska Java 8 do środowiska Java 11.
Przeniesienie dużej aplikacji ze środowiska Java 8 do środowiska Java 11 może wymagać dużo pracy. Potencjalne problemy dotyczą usuniętych interfejsów API, przestarzałych pakietów, korzystania z wewnętrznych interfejsów API, zmian w modułach ładujących klasy oraz zmian w procesie odzyskiwania pamięci. 

Istnieją dwa ogólne podejścia — próba uruchomienia aplikacji w środowisku Java 11 bez ponownego kompilowania lub skompilowanie jej najpierw przy użyciu zestawu JDK 11. Jeśli celem jest jak najszybsze uruchomienie aplikacji, często najlepiej jest po prostu spróbować uruchomić ją w środowisku Java 11. W przypadku biblioteki celem będzie opublikowanie artefaktu skompilowanego i przetestowanego za pomocą zestawu JDK 11.

Przejście środowiska Java 11 jest warte wymaganych nakładów pracy. Dodano w nim nowe funkcje i wprowadzono ulepszenia względem środowiska Java 8. Te funkcje i ulepszenia przekładają się między innymi na usprawnienie uruchamiania oraz poprawę wydajności, użycia pamięci i integracji z kontenerami. Ponadto uzupełniono i zmodyfikowano interfejsy API w sposób zwiększający produktywność deweloperów. 

W tym dokumencie pokrótce omówiono narzędzia do sprawdzania kodu. Wymieniono także potencjalne problemy i zalecenia dotyczące ich rozwiązywania. Warto skorzystać również z innych przewodników, na przykład przewodnika [Oracle JDK Migration Guide](https://docs.oracle.com/en/java/javase/11/migrate/index.html) (Przewodnik po migracji dla zestawu Oracle JDK). Nie omówiono tutaj sposobu przekształcania istniejącego kodu w kod [modułowy](http://openjdk.java.net/projects/jigsaw).  


## <a name="the-toolbox"></a>Zestaw narzędzi

W środowisku Java 11 są dostępne dwa narzędzia, *jdeprscan* i *jdeps*, które pomagają w wykrywaniu potencjalnych problemów. Można uruchamiać je z istniejącymi plikami klas lub plikami jar. To umożliwia ocenę nakładu pracy związanego z przeniesieniem bez potrzeby ponownego kompilowania. 

Narzędzie [jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) sprawdza, czy używane są przestarzałe lub usunięte interfejsy API.
Użycie przestarzałych interfejsów API nie jest problemem blokującym, ale stanowi kwestię do sprawdzenia. Czy jest dostępny zaktualizowany plik jar? Czy trzeba zgłosić problem dotyczący używania przestarzałego interfejsu API? Użycie usuniętych interfejsów API stanowi problem blokujący, który należy rozwiązać przed podjęciem próby uruchomienia w środowisku Java 11.


Narzędzie [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) to analizator zależności klas języka Java. W przypadku użycia go z opcją `--jdk-internals` narzędzie *jdeps* informuje, od których wewnętrznych interfejsów API zależą poszczególne klasy. W środowisku Java 11 można nadal korzystać z wewnętrznych interfejsów API, ale ich zastąpienie powinno być priorytetem. Na stronie wiki zestawu OpenJDK [Java Dependency Analysis Tool](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool) (Narzędzie do analizy zależności języka Java) wymieniono zalecane sposoby zastąpienia niektórych często stosowanych wewnętrznych interfejsów API w zestawach JDK. 

Dostępne są wtyczki narzędzi *jdeps* i *jdeprscan* dla programów Gradle i Maven. Zalecamy dodanie tych narzędzi do skryptów kompilacji. 

> [!div class="mx-tdBreakAll"]
> |Narzędzie|Wtyczka dla programu Gradle|Wtyczka dla programu Maven|
> |-|-|-|
> |jdeps|[jdeps-gradle-plugin](https://github.com/kordamp/jdeps-gradle-plugin)|[Apache Maven JDeps Plugin](https://maven.apache.org/plugins/maven-jdeps-plugin/index.html)|
> |jdeprscan|[jdeprscan-gradle-plugin](https://github.com/kordamp/jdeprscan-gradle-plugin)|[Apache Maven JDeprScan Plugin](https://maven.apache.org/plugins/maven-jdeprscan-plugin/index.html)|

Sam kompilator języka Java, *javac*, to kolejne narzędzie w zestawie. Ostrzeżenia i błędy zgłaszane przez narzędzia *jdeprscan* i *jdeps* będą zwracane przez kompilator.  Zaletą narzędzi *jdeprscan* i *jdeps* jest to, że można uruchamiać je z istniejącymi plikami jar i plikami klas, w tym z bibliotekami innych firm.

Narzędzia *jdeprscan* i *jdeps* nie ostrzegają jednak o użyciu odbicia w celu uzyskania dostępu do hermetyzowanych interfejsów API. Dostęp z użyciem odbicia jest sprawdzany w czasie wykonywania. Ostateczną pewność można więc uzyskać, uruchamiając kod w środowisku Java 11.

### <a name="using-jdeprscan"></a>Korzystanie z narzędzia jdeprscan

Najprostszym sposobem użycia narzędzia [jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) jest wprowadzenie pliku jar z istniejącej kompilacji. Można również wprowadzić katalog, na przykład katalog wyjściowy kompilatora, lub pojedynczą nazwę klasy. Aby uzyskać najpełniejszą listę przestarzałych interfejsów API, należy użyć opcji `--release 11`. Jeśli chcesz określić, którymi z przestarzałych interfejsów API należy zająć się w pierwszej kolejności, zmień to ustawienie na `--release 8`. Interfejs API określony jako przestarzały w środowisku Java 8 prawdopodobnie zostanie usunięty wcześniej niż interfejs API określony jako przestarzały dopiero w nowszej wersji. 

```console
jdeprscan --release 11 my-application.jar
```

Narzędzie *jdeprscan* wygeneruje komunikat o błędzie w przypadku problemu z rozpoznawaniem klasy zależnej.
Na przykład `error: cannot find class org/apache/logging/log4j/Logger`. Zalecane jest dodanie klas zależnych przy użyciu opcji `--class-path` lub użycie ścieżki klas aplikacji, ale narzędzie przeprowadzi skanowanie również bez tych ustawień.
Argument ma formę *&#8209;&#8209;class&#8209;path*. Inne warianty argumentu ścieżki klas nie zadziałają.

```console
jdeprscan --release 11 --class-path log4j-api-2.13.0.jar my-application.jar
error: cannot find class sun/misc/BASE64Encoder
class com/company/Util uses deprecated method java/lang/Double::<init>(D)V
```
Dane wyjściowe pokazują, że klasa `com.company.Util` wywołuje przestarzały konstruktor klasy `java.lang.Double`. Narzędzie javadoc zawiera rekomendację dotyczącą interfejsu API, którego należy użyć zamiast przestarzałego interfejsu API. Niezależnie od nakładu pracy nie można rozwiązać błędu `error: cannot find class sun/misc/BASE64Encoder`, ponieważ dotyczy on usuniętego interfejsu API. Od wersji środowiska Java 8 należy używać interfejsu `java.util.Base64`. 

Uruchom polecenie `jdeprscan --release 11 --list`, aby zobaczyć, jakie interfejsy API zostały określone jako przestarzałe od wersji środowiska Java 8. Aby zobaczyć listę usuniętych interfejsów API, uruchom polecenie `jdeprscan --release 11 --list --for-removal`.

### <a name="using-jdeps"></a>Korzystanie z narzędzia jdeps

Użyj narzędzia [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) z opcją `--jdk-internals`, aby znaleźć zależności od wewnętrznych interfejsów API zestawu JDK. W tym przykładzie potrzebna jest opcja wiersza polecenia `--multi-release 11`, ponieważ plik *log4j-core-2.13.0.jar* to [plik jar zawierający wiele wersji](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files). Jeśli nie użyjesz tej opcji, narzędzie *jdeps* zgłosi błąd, gdy znajdzie plik jar zawierający wiele wersji. Ta opcja określa, którą wersję plików klasy należy sprawdzić. 

```console
jdeps --jdk-internals --multi-release 11 --class-path log4j-core-2.13.0.jar my-application.jar
Util.class -> JDK removed internal API
Util.class -> jdk.base
Util.class -> jdk.unsupported
   com.company.Util        -> sun.misc.BASE64Encoder        JDK internal API (JDK removed internal API)
   com.company.Util        -> sun.misc.Unsafe               JDK internal API (jdk.unsupported)
   com.company.Util        -> sun.nio.ch.Util               JDK internal API (java.base)

Warning: JDK internal APIs are unsupported and private to JDK implementation that are
subject to be removed or changed incompatibly and could break your application.
Please modify your code to eliminate dependence on any JDK internal APIs.
For the most recent update on JDK internal API replacements, please check:
https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

JDK Internal API                         Suggested Replacement
----------------                         ---------------------
sun.misc.BASE64Encoder                   Use java.util.Base64 @since 1.8
sun.misc.Unsafe                          See http://openjdk.java.net/jeps/260   

```

Dane wyjściowe zawierają przydatne sugestie dotyczące wyeliminowania użycia wewnętrznych interfejsów API zestawu JDK. Jeśli jest to możliwe, jest sugerowany zastępczy interfejs API. W nawiasie podawana jest nazwa modułu, w którym znajduje się hermetyzowany pakiet. Można użyć nazwy modułu z opcją `--add-exports` lub `--add-opens`, jeśli wymagane jest jawne [przerwanie hermetyzacji](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66). 

Użycie interfejsów API *sun.misc.BASE64Encoder* lub *sun.misc.BASE64Decoder* spowoduje wystąpienie błędu *java.lang.NoClassDefFoundError* w środowisku Java 11. Kod, w którym są używane te interfejsy API, należy zmodyfikować, aby używać interfejsu API *java.util.Base64*. 

Spróbuj wyeliminować użycie wszelkich interfejsów API z modułu *jdk.unsupported*. W przypadku interfejsów API z tego modułu pojawi się odwołanie do dokumentu [JDK Enhancement Proposal (JEP) 260](http://openjdk.java.net/jeps/260) (Propozycja ulepszenia zestawu JDK JEP 260), zawierającego sugestie dotyczące ich zastąpienia.
W skrócie propozycja JEP 260 mówi, że użycie wewnętrznych interfejsów API będzie obsługiwane do momentu udostępnienia zastępczego interfejsu API. Zatem nawet jeśli w kodzie jest używany wewnętrzny interfejs API zestawu JDK, będzie on działał, przynajmniej przez jakiś czas. Warto zapoznać się z propozycją JEP 260, ponieważ wskazuje ona sposób zastąpienia niektórych wewnętrznych interfejsów API. 
Można na przykład użyć elementów [variable handle](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/VarHandle.html) (dojścia do zmiennej) zamiast niektórych interfejsów API *sun.misc.Unsafe*. 

Narzędzie *jdeps* umożliwia nie tylko skanowanie pod kątem użycia wewnętrznych interfejsów zestawu JDK. Jest też przydatnym narzędziem do analizowania zależności i generowania plików informacji modułu. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html).

### <a name="using-javac"></a>Korzystanie z narzędzia javac

Kompilowanie za pomocą zestawu JDK 11 będzie wymagało aktualizacji skryptów kompilacji, narzędzi, struktur testowych i dołączonych bibliotek. Użyj opcji `-Xlint:unchecked` narzędzia *javac*, aby uzyskać szczegółowe informacje o użyciu wewnętrznych interfejsów API zestawu JDK i innych ostrzeżeniach. Może być konieczne także użycie opcji `--add-opens` lub `--add-reads` w celu uwidocznienia hermetyzowanych pakietów w kompilatorze (zobacz [JEP 261](http://openjdk.java.net/jeps/261)). 

Można rozważyć spakowanie bibliotek w postaci [pliku jar zawierającego wiele wersji](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files). Pliki jar zawierające wiele wersji umożliwiają obsługę środowisk uruchomieniowych Java 8 i Java 11 za pomocą tego samego pliku jar. Zwiększają jednak złożoność kompilacji. Sposób kompilowania plików jar zawierających wiele wersji wykracza poza zakres tego dokumentu. 

## <a name="running-on-java-11"></a>Uruchamianie w środowisku Java 11

Większość aplikacji powinna działać w środowisku Java 11 bez modyfikacji. W pierwszej kolejności należy więc spróbować uruchomić aplikację w środowisku Java 11 bez ponownego kompilowania kodu. Celem takiego uruchomienia jest sprawdzenie ewentualnych ostrzeżeń i błędów zwracanych podczas wykonywania. Tym sposobem można  
szybciej uruchomić aplikację w środowisku Java 11, wykonując tylko minimalne wymagane czynności. 

Większość potencjalnych problemów można rozwiązać bez ponownego kompilowania kodu.
Jeśli problem wymaga rozwiązania w kodzie, należy wprowadzić poprawkę, a następnie znów skompilować kod za pomocą zestawu JDK 8. Jeśli jest to możliwe, należy pracować nad *uruchomieniem* aplikacji w środowisku `java` w wersji 11 przed przejściem do *kompilowania* za pomocą zestawu JDK 11. 

### <a name="check-command-line-options"></a>Sprawdzanie opcji wiersza polecenia

Przed uruchomieniem aplikacji w środowisku Java 11 przejrzyj opcje dostępne w wierszu polecenia. 
Użycie [usuniętych opcji](#unrecognized-options) spowoduje zakończenie działania wirtualnej maszyny Java. Jest to szczególnie istotne, jeśli korzystasz z opcji rejestrowania podczas odzyskiwania pamięci, ponieważ zostały one znacząco zmienione względem wersji Java 8. Narzędzie [JaCoLine](https://jacoline.dev/about) jest przydatne podczas wykrywania problemów z opcjami wiersza polecenia. 

### <a name="check-third-party-libraries"></a>Sprawdzanie bibliotek innych firm

Potencjalnym źródłem problemów są biblioteki innych firm, nad którymi nie masz kontroli. Można aktywnie zaktualizować biblioteki innych firm do nowszych wersji. Można też sprawdzić, co się stanie po uruchomieniu aplikacji, i zaktualizować tylko te biblioteki, które tego wymagają. Problem z aktualizowaniem wszystkich bibliotek do najnowszej wersji polega na tym, że utrudnia to znalezienie głównej przyczyny ewentualnych błędów w aplikacji. Czy przyczyną błędu jest zaktualizowana biblioteka? Czy spowodowała go jakaś zmiana w środowisku uruchomieniowym? Natomiast w przypadku aktualizacji tylko wymaganych bibliotek trudność polega na tym, że rozwiązanie problemu może wymagać kilku iteracji.

Zalecane jest w tym przypadku wprowadzanie jak najmniejszej liczby zmian za jednym razem oraz aktualizowanie bibliotek innych firm w ramach [osobnego zadania](#next-steps). Podczas aktualizowania biblioteki innej firmy najczęściej wskazane jest wybranie najnowszej i najlepszej wersji zgodnej ze środowiskiem Java 11. W zależności od tego, jak stara wersja jest używana obecnie, można postępować ostrożniej, wykonując uaktualnienie do pierwszej wersji zgodnej ze środowiskiem Java w wersji 9+. 

Poza zapoznaniem się z informacjami o wersji można użyć narzędzi *jdeps* i *jdeprscan* do oceny pliku jar. Ponadto zespół OpenJDK Quality Group prowadzi stronę typu wiki [Quality Outreach](https://wiki.openjdk.java.net/display/quality/Quality+Outreach) (Pomoc w zakresie jakości), zawierającą informacje o stanie testowania wielu projektów bezpłatnego oprogramowania typu open source pod kątem zgodności z różnymi wersjami zestawu OpenJDK. 

### <a name="explicitly-set-garbage-collection"></a>Jawnie ustawione odzyskiwanie pamięci

Domyślnym ustawieniem odzyskiwania pamięci w środowisku Java 8 jest równoległe odzyskiwanie pamięci (Parallel GC). Jeśli aplikacja korzysta z ustawienia domyślnego, odzyskiwanie pamięci powinno być jawnie ustawione za pomocą opcji wiersza polecenia `-XX:+UseParallelGC`.
Domyślne ustawienie w środowisku Java 9 zostało zmienione na Garbage First (najpierw elementy bezużyteczne — G1GC). Aby móc rzetelnie porównać działanie aplikacji w środowisku Java 8 i Java 11, ustawienia odzyskiwania pamięci muszą być takie same. Eksperymentowanie z ustawieniami odzyskiwania pamięci należy odłożyć do momentu zweryfikowania działania aplikacji w środowisku Java 11. 

### <a name="explicitly-set-default-options"></a>Jawnie ustawione opcje domyślne

W przypadku uruchomienia na maszynie wirtualnej typu HotSpot ustawienie opcji wiersza polecenia `-XX:+PrintCommandLineFlags` spowoduje zrzut wartości opcji ustawionych przez maszynę wirtualną, a w szczególności opcji domyślnych ustawionych przez moduł odzyskiwania pamięci.
Użyj tej flagi podczas uruchamiania w środowisku Java 8, a następnie użyj zwróconych opcji podczas uruchamiania w środowisku Java 11. W większości przypadków ustawienia domyślne są takie same w środowisku w wersji 8 i 11. Jednak użycie ustawień ze środowiska w wersji 8 zapewnia porównywalność.

Zalecane jest ustawienie opcji wiersza polecenia `--illegal-access=warn`.
W środowisku Java 11 użycie odbicia w celu uzyskania dostępu do wewnętrznego interfejsu API zestawu JDK spowoduje wystąpienie [ostrzeżenia o niedozwolonym dostępie z użyciem odbicia](#warning-an-illegal-reflective-access-operation-has-occurred).
Domyślnie to ostrzeżenie występuje tylko przy pierwszym niedozwolonym dostępie. Po ustawieniu opcji `--illegal-access=warn` ostrzeżenie będzie występować przy *każdym* niedozwolonym dostępie z użyciem odbicia. W przypadku ustawienia opcji *warn* można zatem wykryć więcej przypadków niedozwolonego dostępu. Otrzymasz jednak także wiele nadmiarowych ostrzeżeń.  
Gdy aplikacja będzie już działać w środowisku Java 11, ustaw opcję `--illegal-access=deny`, aby naśladować przyszłe działanie środowiska uruchomieniowego Java. Od wersji środowiska Java 16 ustawienie `--illegal-access=deny` będzie domyślne. 

### <a name="classloader-cautions"></a>Uwagi dotyczące modułów ładujących klasy

W środowisku Java 8 można wykonać rzutowanie modułu ładującego klasy systemowe do modułu `URLClassLoader`. To rozwiązanie jest używane zwykle w przypadku aplikacji i bibliotek, w których wymagane jest wstrzyknięcie klas do ścieżki klas w czasie wykonywania. W środowisku Java 11 hierarchia modułów ładujących klasy uległa zmianie. Moduł ładujący klasy systemowe (inaczej: moduł ładujący klasy aplikacji) stanowi teraz klasę wewnętrzną. Rzutowanie do modułu `URLClassLoader` spowoduje zwrócenie wyjątku `ClassCastException` w czasie wykonywania. W środowisku Java 11 nie ma interfejsu API umożliwiającego dynamiczne rozszerzanie ścieżki klas w czasie wykonywania, ale można to osiągnąć za pomocą odbicia, z oczywistymi zastrzeżeniami dotyczącymi korzystania z wewnętrznych interfejsów API. 

W środowisku Java 11 moduł ładujący klasy rozruchowe ładuje tylko moduły podstawowe. Jeśli utworzysz moduł ładujący klasy z elementem nadrzędnym o wartości null, nie wszystkie klasy platformy mogą zostać znalezione. W środowisku Java 11 należy w takich przypadkach wprowadzić wartość `ClassLoader.getPlatformClassLoader()` zamiast `null` jako element nadrzędny modułu ładującego klasy. 

### <a name="locale-data-changes"></a>Zmiany danych ustawień regionalnych

Zgodnie z propozycją [JEP 252](http://openjdk.java.net/jeps/252) domyślne źródło danych ustawień regionalnych w środowisku Java 11 zmieniło się na repozytorium Common Locale Data Repository organizacji Unicode Consortium. Może mieć to wpływ na zlokalizowane formatowanie. Jeśli jest to konieczne, ustaw właściwość systemu `java.locale.providers=COMPAT,SPI`, aby wrócić do działania ustawień regionalnych ze środowiska Java 8. 

### <a name="potential-issues"></a>Potencjalne problemy

Poniżej wymieniono niektóre typowe problemy, które możesz napotkać. Użyj linków, aby uzyskać szczegółowe informacje na temat tych problemów.

- [Unrecognized VM option](#unrecognized-options) (Nierozpoznana opcja maszyny wirtualnej)
- [Unrecognized option](#unrecognized-options) (Nierozpoznana opcja)
- [VM Warning: Ignoring option](#vm-warnings) (Ostrzeżenie maszyny wirtualnej: ignorowanie opcji)
- [VM Warning: Option &lt;*option*&gt; was deprecated](#vm-warnings) (Ostrzeżenie maszyny wirtualnej: opcja <opcja> jest przestarzała)
- [WARNING: An illegal reflective access operation has occurred](#warning-an-illegal-reflective-access-operation-has-occurred) (Wystąpiła niedozwolona operacja dostępu z użyciem odbicia)
- [java.lang.reflect.InaccessibleObjectException](#javalangreflectinaccessibleobjectexception)
- [java.lang.NoClassDefFoundError](#javalangnoclassdeffounderror)
- [-Xbootclasspath/p is no longer a supported option](#-xbootclasspathp-is-no-longer-a-supported-option) (Opcja Xbootclasspath/p nie jest już obsługiwana)
- [java.lang.UnsupportedClassVersionError](#unsupportedclassversionerror)

#### <a name="unrecognized-options"></a>Nierozpoznane opcje

Jeśli opcja wiersza polecenia została usunięta, aplikacja zwróci błąd `Unrecognized option:` (Nierozpoznana opcja) lub `Unrecognized VM option` (Nierozpoznana opcja maszyny wirtualnej) wraz z nazwą opcji powodującej błąd. Nierozpoznana opcja spowoduje zakończenie działania maszyny wirtualnej.
Opcje, które są przestarzałe, ale nie zostały usunięte, spowodują wystąpienie [ostrzeżenia maszyny wirtualnej](#vm-warnings).

Na ogół opcji, które zostały usunięte, nie można zastąpić, a jedynym rozwiązaniem jest usunięcie opcji z wiersza polecenia. Wyjątek stanowią opcje rejestrowania podczas odzyskiwania pamięci. Rejestrowanie podczas odzyskiwania pamięci zostało [ponownie wdrożone](http://openjdk.java.net/jeps/271) w środowisku Java 9 z zastosowaniem [ujednoliconej struktury rejestrowania wirtualnej maszyny Java](http://openjdk.java.net/jeps/158). Zobacz tabelę „2-2 Mapping Legacy Garbage Collection Logging Flags to the Xlog Configuration” (Tabela 2-2 Mapowanie starszych flag rejestrowania podczas odzyskiwania pamięci na konfigurację Xlog) w sekcji [Enable Logging with the JVM Unified Logging Framework](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5) (Włączanie rejestrowania z zastosowaniem ujednoliconej struktury rejestrowania wirtualnej maszyny Java) w dokumentacji narzędzi środowiska Java SE 11. 

#### <a name="vm-warnings"></a>Ostrzeżenia maszyny wirtualnej

Użycie przestarzałych opcji spowoduje wygenerowanie ostrzeżenia. Opcja jest określana jako przestarzała, gdy zostanie zastąpiona lub gdy przestanie być użyteczna. Tak samo, jak [usunięte opcje](#unrecognized-options), te opcje powinny zostać usunięte z wiersza polecenia.
Ostrzeżenie `VM Warning: Option <option> was deprecated` (Ostrzeżenie maszyny wirtualnej: opcja <opcja> jest przestarzała) oznacza, że ta opcja jest nadal obsługiwana, ale jej obsługa może zostać zakończona w przyszłości. Opcje, które nie są już obsługiwane, spowodują wygenerowanie ostrzeżenia `VM Warning: Ignoring option` (Ostrzeżenie maszyny wirtualnej: ignorowanie opcji).
Opcje, które nie są już obsługiwane, nie mają wpływu na środowisko uruchomieniowe.

Na stronie internetowej [VM Options Explorer](https://chriswhocodes.com/hotspot_option_differences.html) (Eksplorator opcji maszyny wirtualnej) można znaleźć wyczerpującą listę opcji dodanych i usuniętych w środowisku Java od wersji zestawu JDK 7. 

#### <a name="error-could-not-create-the-java-virtual-machine"></a>Błąd: Could not create the Java Virtual Machine (Błąd: nie można utworzyć wirtualnej maszyny Java)

Ten komunikat o błędzie jest zwracany, gdy wirtualna maszyna Java napotka [nierozpoznaną opcję](#unrecognized-options).

#### <a name="warning-an-illegal-reflective-access-operation-has-occurred"></a>OSTRZEŻENIE: An illegal reflective access operation has occurred (Ostrzeżenie: wystąpiła niedozwolona operacja dostępu z użyciem odbicia)

W przypadku użycia odbicia w kodzie Java w celu uzyskania dostępu do wewnętrznego interfejsu API zestawu JDK środowisko uruchomieniowe wygeneruje ostrzeżenie o niedozwolonym dostępie z użyciem odbicia.

```console
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by my.sample.Main (file:/C:/sample/) to method sun.nio.ch.Util.getTemporaryDirectBuffer(int)
WARNING: Please consider reporting this to the maintainers of com.company.Main
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

Oznacza to, że moduł nie wyeksportował pakietu, do którego jest uzyskiwany dostęp z użyciem odbicia. Pakiet jest *hermetyzowany* w module, a więc jest zasadniczo wewnętrznym interfejsem API. To ostrzeżenie można zignorować podczas pierwszej próby uruchamiania w środowisku Java 11.
Środowisko uruchomieniowe Java 11 zezwala na dostęp z użyciem odbicia, aby starszy kod mógł nadal działać.  

Aby rozwiązać problem z tym ostrzeżeniem, poszukaj zaktualizowanego kodu, w którym nie jest używany wewnętrzny interfejs API. Jeśli nie można rozwiązać tego problemu za pomocą zaktualizowanego kodu, można otworzyć dostęp do pakietu przy użyciu opcji wiersza polecenia `--add-exports` lub `--add-opens`.
Te opcje umożliwiają dostęp do niewyeksportowanych typów w jednym module z poziomu innego modułu.

Opcja [`--add-exports`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66) zezwala modułowi docelowemu na dostęp do *publicznych* typów z nazwanego pakietu w module źródłowym. Czasami w kodzie jest używana metoda `setAccessible(true)` w celu uzyskania dostępu do niepublicznych składowych i interfejsów API. Jest to nazywane *odbiciem głębokim*. W takim przypadku użyj opcji [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781), aby umożliwić dostęp do niepublicznych składowych pakietu w kodzie. Jeśli nie masz pewności, czy należy użyć opcji *--add-exports* czy *--add-opens*, zacznij od opcji *--add-exports*. 

Opcje `--add-exports` i `--add-opens` należy traktować jako obejście, a nie jako długoterminowe rozwiązanie.
Użycie tych opcji powoduje przerwanie hermetyzacji systemu modułów, która ma na celu zapobieganie używaniu wewnętrznych interfejsów API zestawu JDK.  W przypadku usunięcia lub zmodyfikowania wewnętrznego interfejsu API aplikacja nie będzie działać.  Dostęp z użyciem odbicia będzie blokowany w środowisku Java 16, z wyjątkiem sytuacji włączenia takiego dostępu za pomocą opcji wiersza polecenia takich jak `--add-opens`.
Aby naśladować to planowane działanie, ustaw opcję `--illegal-access=deny` w wierszu polecenia.

Ostrzeżenie w powyższym przykładzie występuje, ponieważ pakiet `sun.nio.ch` nie został wyeksportowany przez moduł `java.base`. Innymi słowy nie ma polecenia `exports sun.nio.ch;` w pliku `module-info.java` modułu `java.base`. Ten problem można rozwiązać za pomocą opcji `--add-exports=java.base/sun.nio.ch=ALL-UNNAMED`. Klasy niezdefiniowane w module są niejawnie przypisywane do modułu *unnamed* (bez nazwy), którego pełna nazwa to `ALL-UNNAMED`.

#### <a name="javalangreflectinaccessibleobjectexception"></a>java.lang.reflect.InaccessibleObjectException

Ten wyjątek wskazuje, że próbujesz wywołać metodę `setAccessible(true)` względem pola lub metody w hermetyzowanej klasie. Możesz otrzymać także [ostrzeżenie o niedozwolonym dostępie z użyciem odbicia](#warning-an-illegal-reflective-access-operation-has-occurred). Użyj opcji [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781), aby umożliwić dostęp do niepublicznych składowych pakietu w kodzie. Komunikat o wyjątku poinformuje, że moduł „nie otwiera” pakietu dla modułu, w którym próbowano wywołać metodę *setAccessible*. Jeśli ten moduł jest „modułem bez nazwy”, użyj wartości `UNNAMED-MODULE` jako modułu docelowego w opcji *--add-opens*.

```shell
java.lang.reflect.InaccessibleObjectException: Unable to make field private final java.util.ArrayList jdk.internal.loader.URLClassPath.loaders accessible: 
module java.base does not "opens jdk.internal.loader" to unnamed module @6442b0a6

$ java --add-opens=java.base/jdk.internal.loader=UNNAMED-MODULE example.Main
```

#### <a name="javalangnoclassdeffounderror"></a>java.lang.NoClassDefFoundError

Przyczyną błędu *NoClassDefFoundError* jest najprawdopodobniej podzielony pakiet lub odwołanie do usuniętych modułów. 

##### <a name="noclassdeffounderror-caused-by-split-packages"></a>Błąd NoClassDefFoundError powodowany przez podzielone pakiety

Podzielony pakiet to pakiet znajdujący się w więcej niż jednej bibliotece. Objawem problemu z podzielonym pakietem jest nieznalezienie klasy, o której wiesz, że znajduje się na ścieżce klas. 

Ten problem występuje tylko w przypadku używania ścieżki modułów. System modułów języka Java optymalizuje wyszukiwanie klas, ograniczając każdy pakiet do jednego *nazwanego* modułu. Podczas wyszukiwania klas w środowisku uruchomieniowym ścieżka modułów ma priorytet przed ścieżką klas. Jeśli pakiet jest podzielony między ścieżkę modułów a ścieżkę klas, do wyszukiwania klas zostanie użyty tylko moduł. To może prowadzić do wystąpienia błędów `NoClassDefFound`. 

Łatwym sposobem na wykrycie podzielonych pakietów jest wprowadzenie ścieżki modułów i ścieżki klas do narzędzia [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) i użycie ścieżki do plików klas aplikacji jako parametru &lt;path&gt; (ścieżka). W przypadku wykrycia podzielonego pakietu narzędzie jdeps zwróci ostrzeżenie: `Warning: split package: <package-name> <module-path> <split-path>` (Ostrzeżenie: podzielony pakiet: <nazwa-pakietu> <ścieżka-modułu> <podzielona-ścieżka>). 

Ten problem można rozwiązać, używając opcji `--patch-module <module-name>=<path>[,<path>]` w celu dodania podzielonego pakietu do nazwanego modułu. 

##### <a name="noclassdeffounderror-caused-by-using-java-ee-or-corba-modules"></a>Błąd NoClassDefFoundError powodowany przez użycie modułów Java EE lub CORBA

Jeśli aplikacja działa w środowisku Java 8, ale zwraca błąd `java.lang.NoClassDefFoundError` lub `java.lang.ClassNotFoundException`, prawdopodobnie korzysta ona z pakietu pochodzącego z modułów Java EE lub CORBA. Te moduły zostały określone jako przestarzałe w środowisku Java 9, a [usunięte w środowisku Java 11](https://openjdk.java.net/jeps/320). 

Aby rozwiązać ten problem, dodaj do projektu zależność środowiska uruchomieniowego.

> [!div class="mx-tdBreakAll"]
> |Usunięty moduł|Pakiet, którego dotyczy problem|Sugerowana zależność|
> |-|-|-|
> |Java API for XML Web Services (JAX-WS) |java.xml.ws |[JAX WS RI Runtime](https://mvnrepository.com/artifact/com.sun.xml.ws/jaxws-rt) |
> |Java Architecture for XML Binding (JAXB) |java.xml.bind |[JAXB Runtime](https://mvnrepository.com/artifact/org.glassfish.jaxb/jaxb-runtime)|
> |JavaBeans Activation Framework (JAV) |java.activation |[JavaBeans (TM) Activation Framework](https://mvnrepository.com/artifact/javax.activation/activation) |
> |Typowe adnotacje |java.xml.ws.annotation |[Javax Annotation API](https://mvnrepository.com/artifact/javax.annotation/javax.annotation-api)|
> |Common Object Request Broker Architecture (CORBA) |java.corba | [GlassFish CORBA ORB](https://mvnrepository.com/artifact/org.glassfish.corba/glassfish-corba-orb) |
> |Java Transaction API (JTA) |java.transaction | [Java Transaction API](https://mvnrepository.com/artifact/javax.transaction/jta)|

#### <a name="-xbootclasspathp-is-no-longer-a-supported-option"></a>-Xbootclasspath/p is no longer a supported option (Opcja -Xbootclasspath/p nie jest już obsługiwana)

Obsługa opcji `-Xbootclasspath/p` została zakończona. Zamiast tego użyj polecenia cmdlet `--patch-module`. Opcja *--patch-module* została opisana w propozycji [JEP 261](http://openjdk.java.net/jeps/261). Znajdź sekcję zatytułowaną „Patching module content” (Poprawianie zawartości modułu). Opcji *--patch-module* można używać z narzędziami *javac* i *java* w celu zastąpienia lub rozszerzenia klas w module. 

Działanie opcji *--patch-module* polega w zasadzie na wstawieniu modułu poprawki podczas wyszukiwania klas przez system modułów. System modułów znajdzie w pierwszej kolejności klasę z modułu poprawki. Efekt jest taki sam, jak w przypadku dołączenia na początku ścieżki klasy rozruchowej w środowisku Java 8. 

#### <a name="unsupportedclassversionerror"></a>UnsupportedClassVersionError

Ten wyjątek oznacza, że próbujesz uruchomić kod skompilowany za pomocą nowszej wersji środowiska Java w starszej wersji środowiska Java. Na przykład próbujesz uruchomić plik jar skompilowany za pomocą zestawu JDK 13 w środowisku Java 11. 

| Wersja środowiska Java | Wersja formatu pliku klas |
|-|-|
| 8  | 52 |
| 9  | 53 |
| 10 | 54 |
| 11 | 55 |
| 12 | 56 |
| 13 | 57 |

## <a name="next-steps"></a>Następne kroki

Gdy aplikacja będzie już działać w środowisku Java 11, rozważ przeniesienie bibliotek ze ścieżki klas do ścieżki modułów. Poszukaj zaktualizowanych wersji bibliotek, od których jest zależna aplikacja. Wybierz biblioteki modułowe, jeśli są dostępne. W miarę możliwości korzystaj ze ścieżki modułów, nawet jeśli nie planujesz używać modułów w aplikacji. Używanie ścieżki modułów zapewnia lepszą wydajność ładowania klas niż używanie ścieżki klas. 
