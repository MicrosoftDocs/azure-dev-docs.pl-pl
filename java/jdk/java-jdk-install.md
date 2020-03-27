---
title: Instalowanie zestawu JDK Azul Zulu dla platformy Azure i usługi Azure Stack
description: Jak zainstalować zestawy deweloperskie Java (JDK) Azul Zulu do wdrażania na platformie Azure na komputerach z systemem Windows i Linux oraz komputerach Mac
ms.date: 04/19/2019
ms.topic: conceptual
ms.openlocfilehash: f5425bd94a0094fb16649bd3a8069599e30cdba7
ms.sourcegitcommit: efa585ecdcf1cc54a6f0b664fb83cd4f0ccc7b2c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2020
ms.locfileid: "79990416"
---
# <a name="install-the-jdk-for-azure-and-azure-stack"></a>Instalowanie zestawu JDK dla platformy Azure i usługi Azure Stack

Kompilacje zestawu JDK Azul Zulu for Azure-Enterprise Edition to bezpłatne, wieloplatformowe i gotowe do zastosowań produkcyjnych dystrybucje zestawu OpenJDK dla platformy Azure i usługi Azure Stack, wspierane przez firmy Microsoft i Azul Systems. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE.

[Istnieje wiele obsługiwanych typów pakietów do pobrania dla każdego typu systemu operacyjnego klienta](https://www.azul.com/downloads/azure-only/zulu/). Możesz również [pobrać obrazy maszyn wirtualnych z galerii witryny Azure Marketplace](#get-virtual-machine-images-from-the-azure-marketplace-gallery).

> [!NOTE]
> Następujące instrukcje dotyczą 64-bitowej wersji zestawu JDK Java 8. Firma Azul udostępnia również środowisko uruchomieniowe Java (JRE) jako instalację autonomiczną. Środowisko JRE jest dołączone do instalacji zestawu JDK.
>
> Pakiety Java 11 są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-windows"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla systemu Windows

1. Pobierz 64-bitowy zestaw Azul Zulu JDK 8 jako plik MSI:

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-win_x64.msi](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-win_x64.msi)
   * Możesz też poszukać [późniejszej wersji Zulu 8](http://repos.azul.com/azure-only/zulu/packages/zulu-8) pliku *msi*.

   Zapisz go w lokalizacji na kliencie, np. `C:\Users\<your_login>\Downloads`. (Pakiety ZIP są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Przejdź do katalogu i kliknij dwukrotnie pobrany plik MSI, aby rozpocząć instalację.

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-mac"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla komputerów Mac

Te kroki umożliwiają pobranie pliku ZIP na komputer Mac. Dostępna jest również wersja DMG.

1. Pobierz 64-bitowy zestaw Azul Zulu JDK 8 jako plik ZIP:

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-macosx_x64.zip](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-macosx_x64.zip)
   * Możesz też poszukać [późniejszej wersji Zulu 8](http://repos.azul.com/azure-only/zulu/packages/zulu-8) pliku *zip*.

   Zapisz go w lokalizacji na kliencie, np. `/Library/Java/JavaVirtualMachines/`. (Pakiety DMG są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Uruchom program Finder przejdź do katalogu pobierania i kliknij dwukrotnie plik ZIP. Alternatywnie możesz uruchomić okno poleceń terminalu, przejść do katalogu i uruchomić polecenie:

    ```cli
    unzip <name_of_zulu_package>.zip
    ```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-alpine-linux"></a>Pobieranie i instalowanie kompilacji zestawu JDK Azul Zulu for Azure-Enterprise Edition dla systemu Alpine Linux

1. Pobierz 64-bitowy zestaw Azul Zulu JDK 8 jako plik TAR

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-linux_x64.tar.gz](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-linux_x64.tar.gz)
   * Możesz też poszukać [późniejszej wersji Zulu 8](https://repos.azul.com/azure-only/zulu/packages/zulu-8) pliku *tar.gz*.

   Zapisz go w lokalizacji na kliencie, np. `/usr/lib/jvm`. (Pakiety RPM i DEB są również udostępniane na [stronie pobierania oprogramowania dla platformy Azure w witrynie firmy Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Przejdź do katalogu i uruchom następujące polecenie, aby rozpakować i rozwinąć plik:

    ```cli
    tar -xvf <name_of_zulu_package>.tar
    ```

## <a name="confirm-your-installation"></a>Potwierdzanie instalacji

Aby potwierdzić instalację, przejdź do wiersza polecenia i uruchom polecenie `java -version`.

Dane wyjściowe polecenia powinny wyglądać podobnie jak w poniższym przykładzie:

```cli
$ java -version

openjdk version "1.8.0_242"
OpenJDK Runtime Environment (Zulu 8.44.0.11-linux64)-Microsoft-Azure-restricted (build 1.8.0_242-b20)
OpenJDK 64-Bit Server VM (Zulu 8.44.0.11-linux64)-Microsoft-Azure-restricted (build 25.242-b20, mixed mode)
```

## <a name="get-virtual-machine-images-from-the-azure-marketplace-gallery"></a>Pobieranie obrazów maszyn wirtualnych z galerii witryny Azure Marketplace

Obraz maszyny wirtualnej możesz pobrać dla następujących platform:

* [Azul Zulu: Java 8 w systemie Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)
* [Azul Zulu: Java 8 w systemie Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-windows-2019)
* [Azul Zulu: Java 11 w systemie Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-ubuntu-1804)
* [Azul Zulu: Java 11 w systemie Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-windows-2019)

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

W przypadku środowiska Java 13 (wersja zapoznawcza) uruchom:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-13-azure-jdk
```

**Aby zaktualizować pakiet Zulu JDK 8 z repozytorium Yum:**

```cli
sudo yum -q -y install zulu-8-azure-jdk
```

(Zmień numer wersji w powyższym poleceniu, jeśli używasz innej wersji).

**Aby usunąć pakiet Zulu JDK 8 z repozytorium Yum:**

```cli
sudo yum -y erase zulu-8-azure-jdk
```

(Zmień numer wersji w powyższym poleceniu, jeśli używasz innej wersji).

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

W przypadku środowiska Java 13 (wersja zapoznawcza) uruchom:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-13-azure-jdk
```

**Aby zaktualizować pakiet Zulu JDK 8 z repozytorium apt-get:**

```cli
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

Poprzednia wersja zostanie automatycznie usunięta.
(Zmień numer wersji w powyższym poleceniu, jeśli używasz innej wersji).

**Aby usunąć pakiet Zulu JDK 8 z repozytorium apt-get:**

```cli
sudo apt-get -y purge zulu-8-azure-jdk
```

(Zmień numer wersji w powyższym poleceniu, jeśli używasz innej wersji).

Aby uzyskać bardziej szczegółowe wskazówki dotyczące przygotowywania i instalowania zestawów JDK Azul Zulu do programowania na platformie Azure oraz zarządzania nimi, przeczytaj [oficjalne dokumenty Zulu](https://docs.azul.com/zulu/zuludocs/index.htm).
