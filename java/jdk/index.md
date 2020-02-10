---
title: Zestawy Java JDK i długoterminowa obsługa tworzenia oprogramowania na platformie Azure
description: Pliki do pobrania oraz opis obsługi tworzenia i uruchamiania aplikacji języka Java na platformie Azure.
ms.topic: conceptual
ms.date: 04/09/2019
ms.custom: seo-java-september2019
ms.openlocfilehash: 3e0bb0e457d79f337c36b0c5649f471061a2a162
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77001293"
---
# <a name="java-long-term-support-for-azure-and-azure-stack"></a>Długoterminowa obsługa języka Java dla platformy Azure i usługi Azure Stack

Deweloperzy języka Java na platformie Azure i w usłudze Azure Stack mogą tworzyć i uruchamiać produkcyjne aplikacje języka Java przy użyciu kompilacji zestawów JDK [Azul Zulu for Azure - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) bez ponoszenia dodatkowych kosztów obsługi. Na platformie Azure można używać dowolnego środowiska uruchomieniowego języka Java, ale w przypadku używania oprogramowania Zulu uzyskuje się bezpłatne aktualizacje w ramach konserwacji oraz można zgłaszać problemy do pomocy technicznej firmy Microsoft.

> [!div class="nextstepaction"]
> [Pobieranie i instalowanie oprogramowania Java](java-jdk-install.md)

## <a name="long-term-support-lts"></a>Obsługa długoterminowa (LTS, Long Term Support)

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/#java11)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/#java8)
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/#java7)

## <a name="technical-preview"></a>Techniczna wersja zapoznawcza

* [Java 12](https://www.azul.com/downloads/azure-only/zulu/#java12)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>Co to jest zestaw Zulu OpenJDK for Azure?

Kompilacje zestawu OpenJDK Azul Zulu for Azure-Enterprise Edition to bezpłatne, wieloplatformowe i gotowe do zastosowań produkcyjnych dystrybucje zestawu OpenJDK dla platformy Azure i usługi Azure Stack, wspierane przez firmy Microsoft i Azul Systems. Oto cechy tych dystrybucji:

* Kompilacje zestawu OpenJDK w 100% open source, spakowane jako zestawy Java Development Kit (JDK), środowiska uruchomieniowe języka Java (JRE) i bezobsługowe środowiska JRE. Te pliki binarne to w pełni zgodne komercyjne kompilacje środowiska Java Standard Edition (SE), których można używać z aplikacjami lub składnikami języka Java na platformie Azure i w usłudze Azure Stack.
* Udostępniane z długoterminową obsługą, obejmującą poprawki błędów, ulepszenia wydajności i poprawki zabezpieczeń.
* Umożliwiają tworzenie i uruchamianie aplikacji języka Java w systemach Windows, Linux i MacOS.
* Dostępne jako obrazy kontenerów w usłudze Docker Hub oraz jako maszyny wirtualne (Windows i Linux) w witrynie Azure Marketplace.
* Używane na platformie Microsoft Azure do obsługiwania wielu usług, takich jak:
  * App Service dla systemu Windows
  * App Service dla systemu Linux
  * Functions
  * Service Fabric
  * HDInsight
  * Wyszukiwanie
  * Azure DevOps
  * Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Obsługiwane wersje języka Java i harmonogram aktualizacji

Firma Azul Systems udostępnia w pełni obsługiwane kompilacje [Azul Zulu for Azure - Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) dla wszystkich wersji środowiska Java z obsługą długoterminową, począwszy od środowisk Java SE 7, 8 i 11. Więcej informacji można znaleźć w [informacjach prasowych firmy Azul](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack).

|Java SE LTS  |Obsługa do  |
|---------|----------|
|[![Obsługiwana wersja języka Java — Java 7](../media/jdk/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/#java7) |Lipiec 2023 r. |
|[![Obsługiwana wersja języka Java — Java 8](../media/jdk/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/#java8) |Marzec 2025 r.|
|[![Obsługiwana wersja języka Java — Java 11](../media/jdk/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/#java11) |Wrzesień 2026 r.|
|[![Obsługiwana wersja języka Java — Java 12](../media/jdk/supported-java-versions-java-12.png)]() |**(WERSJA ZAPOZNAWCZA)**|

Dla tych wydań zestawu JDK są udostępniane kwartalne aktualizacje zabezpieczeń, poprawki błędów oraz krytyczne aktualizacje i poprawki poza pasmem, zależnie od potrzeb.  Ta obsługa uwzględnia wsteczne adaptacje dla środowisk Java 7 i 8 obejmujące aktualizacje zabezpieczeń i poprawki błędów zgłaszane w nowszych wersjach środowiska Java (takich jak Java 11), co zapewnia ciągłą stabilność i bezpieczeństwo starszych wersji środowiska Java.  Klienci korzystający z platformy Azure mogą uzyskiwać te aktualizacje zabezpieczeń i poprawki błędów platformy bez ponoszenia żadnych nieplanowanych opłat za subskrypcję środowiska Java SE.

Firma Azul Systems udostępnia [plan rozwoju środowiska Java SE](https://www.azul.com/products/azul_support_roadmap/) dla tych wersji.

## <a name="benefits-for-developers"></a>Korzyści dla deweloperów

Wydania zestawu JDK Azul Zulu for Azure-Enterprise Edition mają następujące cechy:

1. Wsparcie i obsługa przez firmy Microsoft i Azul Systems

   * Pliki binarne oprogramowania Zulu są gotowe do zastosowań produkcyjnych oraz wspierane przez firmy Microsoft i Azul Systems
   * Oprogramowanie Zulu jest objęte bezpłatną obsługą długoterminową (LTS) dla środowisk Java 7, 8 i 11. (Obsługa LTS będzie też dostępna dla środowiska Java 17). Można uaktualniać wersje środowiska Java tylko w razie potrzeby.
   * Środowisko Java 7 jest obsługiwane do lipca 2023 r. Środowiska Java 8 i 11 będą obsługiwane dłużej niż do 2024 r.
   * Firma Microsoft uruchamia oprogramowanie Zulu wewnętrznie na maszynach obsługujących wiele usług platformy Azure.

2. Gotowe do zastosowań produkcyjnych

   * Kompilacje zestawu OpenJDK w 100% open source.
   * Bezpośrednie zamienniki dla wielu dystrybucji środowiska Java SE.
   * Oprogramowanie JDK, JRE i JRE-headless.
   * Środowiska Java 7, 8 i 11.
   * Zweryfikowana zgodność ze specyfikacjami środowiska Java SE przy użyciu zestawu OpenJDK Community Technology Compatibility Kit (TCK).
   * Deweloperzy będą nadal otrzymywać aktualizacje produkcyjne dla środowiska Java SE, w tym poprawki błędów, ulepszenia wydajności i poprawki zabezpieczeń dla środowisk Java SE 7, 8 i 11.

3. Obsługa wielu platform. Oprogramowani Zulu obsługuje pliki binarne dla wielu platform i wersji, w tym:

   * Klient systemu Windows
     * 10
     * 8.1
     * 8, 7
   * Windows Server
     * 2016 R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux, w tym
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * Dostarczane w wielu typach pakietów:
     * MSI, ZIP, TAR, DEB, RPM i DMG

    Certyfikowane obrazy kontenerów platformy Docker dla środowisk Zulu JDK, JRE i JRE-headless dla wielu podstawowych obrazów systemów operacyjnych są dostępne na platformie Docker.

    Usługa Hub:

    * Zestaw [JDK](https://hub.docker.com/_/microsoft-java-jdk).
    * [JRE](https://hub.docker.com/_/microsoft-java-jre)
    * [JRE-headless](https://hub.docker.com/_/microsoft-java-jre-headless)

4. Brak opłat

   * Firma Microsoft zapewnia wszystko, co jest potrzebne do tworzenia i skalowania aplikacji języka Java na platformie Azure bez ponoszenia kosztów. W ramach oprogramowania Zulu będziesz uzyskiwać bezpłatne aktualizacje zabezpieczeń i poprawki błędów platformy dla aplikacji języka Java bez żadnych opłat.
   * [Narzędzia Java Flight Recorder i Mission Control](java-jdk-flight-recorder-and-mission-control.md) są dostępne w oprogramowaniu Zulu dla środowisk Java 8, 11 i 12 (wersja zapoznawcza).

5. Techniczna wersja zapoznawcza dla wersji innych niż LTS

   * Techniczne wersje zapoznawcze umożliwiają stopniowe testowanie nowych funkcji udostępnianych w wersjach krótkoterminowych, które ostatecznie zostaną włączone do środowiska Java 17 LTS.

6. Zmiany w zestawie OpenJDK są przesyłane do lokalizacji nadrzędnej

   * Osoby zatwierdzające w firmie Azul Systems wypychają zmiany oprogramowania Zulu do zestawu OpenJDK, dzięki czemu repozytorium nadrzędne jest wszechstronne i kompletne.

Jak zawsze, deweloperzy języka Java mogą używać na platformie Azure własnych środowisk uruchomieniowych Java (takich jak Oracle JDK i Red Hat JDK) oraz korzystać z bezpiecznej infrastruktury i wielofunkcyjnych usług. Deweloperzy języka Java mają też dostęp do wersji produkcyjnej środowiska Oracle Java SE na potrzeby uruchamiania obciążeń języka Java na maszynach wirtualnych z systemem Windows lub Linux na platformie Azure.

## <a name="use-for-local-development"></a>Użytek na potrzeby lokalnego tworzenia oprogramowania 

Deweloperzy mogą [pobierać](https://www.azul.com/downloads/azure-only/zulu/) zestawy Java JDK dla platformy Azure i usługi Azure Stack do używania w lokalnych środowiskach deweloperskich. Pliki do pobrania są dostępne dla systemów Windows, Linux i macOS. Deweloperzy pracujący w systemie Linux mogą też pobierać pakiety za pomocą menedżerów pakietów [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) i [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo).

Aby uzyskać dodatkowe wskazówki, zobacz [Obrazy platformy Docker dla platformy Azure](java-jdk-docker-images.md).