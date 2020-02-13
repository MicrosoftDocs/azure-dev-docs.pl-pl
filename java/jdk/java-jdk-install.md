---
title: Instalowanie zestawu JDK Azul Zulu dla platformy Azure i usługi Azure Stack
description: Jak zainstalować zestawy deweloperskie Java (JDK) Azul Zulu do wdrażania na platformie Azure na komputerach z systemem Windows i Linux oraz komputerach Mac
ms.date: 04/19/2019
ms.topic: conceptual
ms.openlocfilehash: d0d9c1263234baf515ce81ede49b0ac82e8491ab
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999928"
---
# <a name="install-the-jdk-for-azure-and-azure-stack"></a>Instalowanie zestawu JDK dla platformy Azure i usługi Azure Stack

Kompilacje zestawu JDK Azul Zulu for Azure-Enterprise Edition to bezpłatne, wieloplatformowe i gotowe do zastosowań produkcyjnych dystrybucje zestawu OpenJDK dla platformy Azure i usługi Azure Stack, wspierane przez firmy Microsoft i Azul Systems. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE.

[Istnieje wiele obsługiwanych typów pakietów do pobrania dla każdego typu systemu operacyjnego klienta](https://www.azul.com/downloads/azure-only/zulu/). Możesz również uzyskać obraz maszyny wirtualnej z galerii portalu Azure Marketplace dla następujących platform:

  * [Azul Zulu: Java 8 w systemie Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)
  * [Azul Zulu: Java 8 w systemie Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-windows-2019)
  
  * [Azul Zulu: Java 11 w systemie Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-ubuntu-1804)
  * [Azul Zulu: Java 11 w systemie Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-windows-2019)


> [!NOTE]
> Te instrukcje dotyczą 64-bitowej wersji zestawu JDK Java 8. Firma Azul udostępnia również środowisko uruchomieniowe Java (JRE) jako instalację autonomiczną. Środowisko JRE jest dołączone do instalacji zestawu JDK.
>
>  Pakiety Java 11 są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-windows"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla systemu Windows 

1. [Pobierz wersję 64-bitową zestawu  JDK 8 Azul Zulu jako plik MSI ](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-win_x64.msi) do lokalizacji na kliencie, np. `C:\Users\<your_login>\Downloads`. (Pakiety ZIP są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Przejdź do katalogu i kliknij dwukrotnie pobrany plik MSI, aby rozpocząć instalację.

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-mac"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla komputerów Mac 

Te kroki umożliwiają pobranie pliku ZIP na komputer Mac. Dostępna jest również wersja DMG.

1. [Pobierz wersję 64-bitową zestawu JDK 8 Azul Zulu jako plik ZIP ](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-macosx_x64.zip) do lokalizacji na kliencie, np. `/Library/Java/JavaVirtualMachines/`. (Pakiety DMG są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Uruchom program Finder przejdź do katalogu pobierania i kliknij dwukrotnie plik ZIP. Alternatywnie możesz uruchomić okno poleceń terminalu, przejść do katalogu i uruchomić polecenie:

```cli
unzip <name_of_zulu_package>.zip
```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-alpine-linux"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla systemu Alpine Linux

1. [Pobierz wersję 64-bitową zestawu JDK 8 Azul Zulu jako plik TAR ](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-linux_x64.tar.gz) do lokalizacji na kliencie, np. `/usr/lib/jvm`. (Pakiety RPM i DEB są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Przejdź do katalogu i uruchom następujące polecenie, aby rozpakować i rozwinąć plik:

    ```cli
    tar -xvf <name_of_zulu_package>.tar
    ```

## <a name="confirm-your-installation"></a>Potwierdzanie instalacji

Aby potwierdzić instalację, przejdź do wiersza polecenia i uruchom polecenie `java -version`.

Dane wyjściowe polecenia powinny wyglądać następująco:

```cli
$ java -version

openjdk version "1.8.0_212"
OpenJDK Runtime Environment (Zulu 8.38.0.13-macosx)-Microsoft-Azure-restricted (build 1.8.0_212-b04)
OpenJDK 64-Bit Server VM (Zulu 8.38.0.13-macosx)-Microsoft-Azure-restricted (build 25.212-b04, mixed mode)

```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdks-from-a-yum-repository"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition z repozytorium Yum

Zestawy JDK Azul Zulu są dostępne w [repozytorium Yum](https://repos.azul.com/azure-only/zulu-azure.repo) firmy Azul.

**Aby zainstalować zestaw JDK Azul Zulu dla środowiska Java 8, uruchom następujące polecenia w interfejsie wiersza polecenia:**

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-8-azure-jdk
```

W przypadku środowiska Java 11 uruchom:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-11-azure-jdk
```

W przypadku środowiska Java 12 (wersja zapoznawcza) uruchom:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-12-azure-jdk
```

**Aby zaktualizować pakiet Zulu JDK 8 z repozytorium Yum:**

```cli
sudo yum -q -y install zulu-8-azure-jdk
```

(Zmień numer wersji w powyższym poleceniu, jeśli używasz wersji 11 lub 12.)

**Aby usunąć pakiet Zulu JDK 8 z repozytorium Yum:**

```cli
sudo yum -y erase zulu-8-azure-jdk
```
(Zmień numer wersji w powyższym poleceniu, jeśli używasz wersji 11 lub 12.)

## <a name="download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository"></a>Pobieranie i instalowanie zestawów JDK Azul Zulu z repozytorium apt-get

Zestawy JDK Azul Zulu są również dostępne w [repozytorium apt-get](https://repos.azul.com/azure-only/zulu/apt) firmy Azul.

**Aby zainstalować zestaw JDK Azul Zulu dla środowiska Java 8 przy użyciu polecenia apt-get, uruchom następujące polecenia w interfejsie wiersza polecenia:**

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

W przypadku środowiska Java 11 uruchom:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-11-azure-jdk
```

W przypadku środowiska Java 12 (wersja zapoznawcza) uruchom:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-12-azure-jdk
```

**Aby zaktualizować pakiet Zulu JDK 8 z repozytorium apt-get:**

```cli
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

Poprzednia wersja zostanie automatycznie usunięta.
(Zmień numer wersji w powyższym poleceniu, jeśli używasz wersji 11 lub 12.)

**Aby usunąć pakiet Zulu JDK 8 z repozytorium apt-get:**

```cli
sudo apt-get -y purge zulu-8-azure-jdk
```

(Zmień numer wersji w powyższym poleceniu, jeśli używasz wersji 11 lub 12.)

Aby uzyskać bardziej szczegółowe wskazówki dotyczące przygotowywania i instalowania zestawów JDK Azul Zulu do programowania na platformie Azure oraz zarządzania nimi, przeczytaj [oficjalne dokumenty Zulu](https://docs.azul.com/zulu/zuludocs/index.htm).

