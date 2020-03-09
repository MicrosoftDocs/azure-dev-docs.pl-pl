---
title: Migrowanie aplikacji Java na platformę Azure
description: W tym temacie omówiono zalecane strategie migracji aplikacji Java na platformę Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: 7025393f45a32a42fcd2ae3dafee6274a4ba3cbc
ms.sourcegitcommit: aceed8548ad4529a81d83eb15a095edc8607cac5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77440857"
---
# <a name="migrate-java-applications-to-azure"></a>Migrowanie aplikacji Java na platformę Azure

W tym temacie omówiono zalecane strategie migracji aplikacji Java na platformę Azure.

## <a name="identifying-application-type"></a>Identyfikowanie typu aplikacji

Przed wybraniem lokalizacji docelowej w chmurze dla aplikacji Java musisz zidentyfikować typ aplikacji. Większość aplikacji Java ma jeden z następujących typów:

* [Aplikacje Spring Boot/JAR](#spring-boot--jar-applications)
* [Spring Cloud/mikrousługi](#spring-cloud--microservices)
* [Aplikacje internetowe](#web-applications)
* [Aplikacje Java EE](#java-ee-applications)
* [Batch/zaplanowane zadania](#batch--scheduled-jobs)

Te typy zostały opisane w poniższych sekcjach.

### <a name="spring-boot--jar-applications"></a>Aplikacje Spring Boot/JAR

Wiele nowszych aplikacji wywołuje się bezpośrednio z poziomu wiersza polecenia. Aplikacje te nadal obsługują żądania internetowe, ale zamiast polegać na serwerze aplikacji zapewniającym obsługę żądań HTTP, dołączają komunikację HTTP i wszystkie pozostałe zależności bezpośrednio do pakietu aplikacji. Tego rodzaju aplikacje są często kompilowane za pomocą struktur, takich jak Spring Boot, Dropwizard, Micronaut, MicroProfile, Vert.x itd.

Aplikacje te są pakowane w archiwa z rozszerzeniem *JAR* (pliki JAR).

### <a name="spring-cloud--microservices"></a>Spring Cloud/mikrousługi

Styl architektury mikrousług to metoda tworzenia pojedynczej aplikacji jako pakietu małych usług, z których każda działa we własnym procesie i komunikuje się za pomocą uproszczonych mechanizmów, często interfejsu API zasobów HTTP. Usługi te są kompilowane w oparciu o funkcje biznesowe i niezależnie wdrażane przez całkowicie zautomatyzowane maszyny wdrożeniowe. Scentralizowane zarządzanie tymi usługami jest ograniczone do absolutnego minimum. Mogą one być pisane w różnych językach programowania i korzystać z różnych technologii magazynowania danych. Tego rodzaju usługi są często kompilowane za pomocą struktur, takich jak platforma Spring Cloud.

Usługi te są pakowane w wiele aplikacji z rozszerzeniem *JAR* (pliki JAR).

### <a name="web-applications"></a>Aplikacje internetowe

Aplikacje internetowe działają wewnątrz kontenera [Servlet](https://en.wikipedia.org/wiki/Java_servlet). Niektóre używają bezpośrednio interfejsów API serwletu, a wiele z nich korzysta z dodatkowych struktur hermetyzujących interfejsy API serwletu, takich jak Apache Struts, Spring MVC, JavaServer Faces (JSF) itd.

Aplikacje internetowe są pakowane w archiwa z rozszerzeniem *WAR* (pliki WAR).

### <a name="java-ee-applications"></a>Aplikacje Java EE

Aplikacje Java EE (nazywane również aplikacjami J2EE lub od niedawna aplikacjami Jakarta EE) mogą zawierać niektóre lub wszystkie elementy aplikacji internetowych albo nie zawierać ich wcale. Mogą również zawierać i zużywać wiele dodatkowych składników zdefiniowanych w [specyfikacji języka Java EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition).

Aplikacje Java EE można pakować jako archiwa z rozszerzeniem *EAR* (pliki EAR) lub jako archiwa z rozszerzeniem *WAR* (pliki WAR).

Aplikacje Java EE należy wdrażać na serwerach aplikacji zgodnych z technologią Java EE (takich jak WebLogic, WebSphere, WildFly, GlassFish, Payara itd.).

Aplikacje polegające tylko na funkcjach udostępnianych przez specyfikację Java EE (tzn. aplikacje niezależne od serwera aplikacji) można migrować z jednego zgodnego serwera aplikacji na inny. Jeśli aplikacja jest zależna od określonego serwera aplikacji, może być konieczne wybranie lokalizacji docelowej usługi platformy Azure pozwalającej na hostowanie tego serwera aplikacji.

### <a name="batch--scheduled-jobs"></a>Batch/zaplanowane zadania

Niektóre aplikacje są przeznaczone do uruchamiania na krótko, wykonywania określonego obciążenia, a następnie kończenia pracy, a nie do czekania na żądania lub wprowadzenie informacji przez użytkownika. Czasami takie zadania muszą być uruchamiane jednokrotnie lub w regularnych, zaplanowanych odstępach czasu. W środowiskach lokalnych tego rodzaju zadania są często wywoływane z pliku crontab serwera.

Aplikacje te są pakowane w archiwa z rozszerzeniem *JAR* (pliki JAR).

> [!NOTE]
> Jeśli aplikacja uruchamia zaplanowane zadania przy użyciu harmonogramu (np. Spring Batch lub Quartz), zdecydowanie zalecamy faktoryzację takich zadań, aby można było uruchamiać je poza aplikacją. Jeśli aplikacja jest skalowana na wiele wystąpień w chmurze, to samo zadanie zostanie uruchomione więcej niż raz. Ponadto jeśli mechanizm planowania używa lokalnej strefy czasowej hosta, podczas skalowania aplikacji na różne regiony może wystąpić niepożądane zachowanie.

## <a name="selecting-the-target-azure-service-destination"></a>Wybieranie lokalizacji docelowej usługi platformy Azure

W poniższych sekcjach pokazano, które lokalizacje docelowe usługi spełniają wymagania aplikacji oraz jakie nakładają obowiązki.

### <a name="feature-grid"></a>Siatka funkcji

Poniższa siatka umożliwia zidentyfikowanie lokalizacji docelowych, które obsługują wymagane typy i funkcje aplikacji.

|   |Aplikacja<br>Usługa<br>Java SE|Aplikacja<br>Usługa<br>Tomcat|Aplikacja<br>Usługa<br>WildFly|Azure<br>Spring<br>Chmurowa|AKS|Maszyny wirtualne|
|---|---|---|---|---|---|---|
| Aplikacje Spring Boot/JAR                                    |&#x2714;|        |        |        |&#x2714;|&#x2714;|
| Spring Cloud/mikrousługi                                      |        |        |        |&#x2714;|&#x2714;|&#x2714;|
| Aplikacje internetowe                                                  |        |&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| Aplikacje Java EE                                              |        |        |&#x2714;|        |&#x2714;|&#x2714;|
| Komercyjne serwery aplikacji<br>(na przykład WebLogic lub WebSphere) |        |        |        |        |&#x2714;|&#x2714;|
| Długoterminowa trwałość lokalnego systemu plików                         |&#x2714;|&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| Klastrowanie na poziomie serwera aplikacji                               |        |        |        |        |&#x2714;|&#x2714;|
| Batch/zaplanowane zadania                                            |        |        |        |&#x2714;|&#x2714;|&#x2714;|

### <a name="ongoing-responsibility-grid"></a>Siatka stałych obowiązków

Poniższa siatka umożliwia zrozumienie obowiązków każdej lokalizacji docelowej w zespole po migracji.

Zespół jest na stałe odpowiedzialny za zadania oznaczone symbolem „&#x1F449;”. Zalecamy zaimplementowanie niezawodnego, wysoce zautomatyzowanego procesu do realizacji wszystkich obowiązków tego typu. 

> [!NOTE]
> Nie jest to wyczerpująca lista obowiązków.

|   | App Service | Azure Spring Cloud | AKS | Maszyny wirtualne |
|---|---|---|---|---|
| Aktualizowanie bibliotek<br>(w tym korygowanie luk w zabezpieczeniach)                 | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |
| Aktualizowanie serwera aplikacji<br>(w tym korygowanie luk w zabezpieczeniach)    | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Aktualizowanie środowiska uruchomieniowego Java<br>(w tym korygowanie luk w zabezpieczeniach)          | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Wyzwalanie aktualizacji platformy Kubernetes<br>(wykonywane przez platformę Azure za pomocą wyzwalacza ręcznego) | Nie dotyczy         | Nie dotyczy         | &#x1F449;   | Nie dotyczy       |
| Uzgadnianie zmian w interfejsie API platformy Kubernetes, które nie są zgodne z poprzednimi wersjami                  | Nie dotyczy         | Nie dotyczy         | &#x1F449;   | Nie dotyczy       |
| Aktualizowanie podstawowego obrazu kontenera<br>(w tym korygowanie luk w zabezpieczeniach)      | Nie dotyczy         | Nie dotyczy         | &#x1F449;   | Nie dotyczy       |
| Aktualizowanie systemu operacyjnego<br>(w tym korygowanie luk w zabezpieczeniach)      | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Wykrywanie i ponowne uruchamianie wystąpień zakończonych niepowodzeniem                                   | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Implementowanie opróżniania i stopniowego ponownego uruchamiania w celu zainstalowania aktualizacji                       | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Zarządzanie infrastrukturą                                                   | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Monitorowanie alertów i zarządzanie nimi                                             | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |

Jeśli w ramach aplikacji wdrażasz kontener serwletu (np. Spring Boot), traktuj go jak bibliotekę, co oznacza, że zawsze ponosisz za niego odpowiedzialność.

## <a name="ensuring-on-premises-connectivity"></a>Zapewnianie łączności lokalnej

Jeśli aplikacja wymaga dostępu do dowolnych usług lokalnych, musisz aprowizować jedną z usług łączności platformy Azure. Aby uzyskać więcej informacji, zobacz [Wybieranie rozwiązania do łączenia sieci lokalnej z platformą Azure](/azure/architecture/reference-architectures/hybrid-networking/). Możesz również przeprowadzić refaktoryzację aplikacji, aby korzystać z publicznie dostępnych interfejsów API uwidacznianych przez Twoje zasoby lokalne.

Ten proces należy wykonać przed rozpoczęciem migracji.

## <a name="inventory-current-capacity-and-resource-usage"></a>Bieżąca pojemność i użycie zasobów magazynu

Zapisz składniki sprzętowe bieżących serwerów produkcyjnych, a także średnią i szczytową liczbą żądań oraz użycie zasobów. Informacje te będą potrzebne do aprowizacji zasobów w lokalizacji docelowej usługi.

## <a name="migration-guidance"></a>Wskazówki dotyczące migracji

Poniższe siatki umożliwiają znajdowanie wskazówek dotyczących migracji według typu aplikacji i lokalizacji docelowej usługi platformy Azure.

**Aplikacje Java**

W poniższych wierszach możesz znaleźć typ aplikacji Java i kolumny zawierające lokalizację docelową usługi platformy Azure, w której będzie hostowana Twoja aplikacja.

|Lokalizacja docelowa&nbsp;→<br><br>Typ&nbsp;aplikacji&nbsp;↓|Aplikacja<br>Usługa<br>Java SE|Aplikacja<br>Usługa<br>Tomcat|Aplikacja<br>Usługa<br>WildFly|Azure<br>Spring<br>Chmurowa|AKS|Maszyny wirtualne|
|---|---|---|---|---|---|---|
| Spring Boot /<br>Aplikacje JAR | [dostępne][5] | planowane        | planowane | planowane | planowane        | planowane |
| Spring Cloud /<br>mikrousługi   | Nie dotyczy            | Nie dotyczy            | Nie dotyczy     | planowane | planowane        | planowane |
| Aplikacje internetowe<br>na serwerze Tomcat     | Nie dotyczy            | [dostępne][2] | Nie dotyczy     | Nie dotyczy     | [dostępne][3] | planowane |

**Aplikacje Java EE**

W poniższych wierszach możesz znaleźć typ aplikacji Java EE działającej na określonym serwerze aplikacji. W kolumnach znajdziesz lokalizację docelową usługi platformy Azure, w której będzie hostowana Twoja aplikacja.

|Lokalizacja docelowa&nbsp;→<br><br>Serwer aplikacji&nbsp;↓|Aplikacja<br>Usługa<br>Java SE|Aplikacja<br>Usługa<br>Tomcat|Aplikacja<br>Usługa<br>WildFly|Azure<br>Spring<br>Chmurowa|AKS|Maszyny wirtualne|
|---|---|---|---|---|---|---|
| WildFly /<br>JBoss AS | Nie dotyczy | Nie dotyczy | planowane | Nie dotyczy | planowane | planowane        |
| WebLogic              | Nie dotyczy | Nie dotyczy | planowane | Nie dotyczy | planowane | [dostępne][4] |
| WebSphere             | Nie dotyczy | Nie dotyczy | planowane | Nie dotyczy | planowane | planowane        |
| JBoss EAP             | Nie dotyczy | Nie dotyczy | planowane | Nie dotyczy | Nie dotyczy     | planowane        |

<!-- reference links, for use with tables -->
[1]: media/migration-overview/logo_azure.svg
[2]: migrate-tomcat-to-tomcat-app-service.md
[3]: migrate-tomcat-to-containers-on-azure-kubernetes-service.md
[4]: migrate-weblogic-to-virtual-machines.md
[5]: migrate-java-se-to-java-se-app-service.md