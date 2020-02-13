---
title: Instalowanie zestawu Azure Toolkit for IntelliJ
description: Dowiedz się, jak zainstalować zestaw Azure Toolkit for IntelliJ, aby tworzyć i wdrażać aplikacje w chmurze na platformie Azure.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 5b5cb3663b2dbb6c699635d1d58b077d851b2657
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999480"
---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Instalowanie zestawu Azure Toolkit for IntelliJ

Zestaw Azure Toolkit for IntelliJ udostępnia szablony i funkcje, które umożliwiają łatwe tworzenie, opracowywanie, testowanie i wdrażanie aplikacji w chmurze na platformie Azure przy użyciu środowiska projektowego IntelliJ IDEA.

> [!NOTE] 
> 
> Zestaw Azure Toolkit for IntelliJ to projekt open-source, którego kod źródłowy jest dostępny w ramach licencji MIT w witrynie projektu w serwisie GitHub pod następującym adresem URL: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

Istnieją dwie metody instalowania zestawu Azure Toolkit for IntelliJ: przy użyciu okna dialogowego **Settings** (Ustawienia) oraz przy użyciu menu **Configure** (Konfiguruj) na ekranie startowym. Obie metody instalacji zostaną przedstawione w poniższych krokach.

## <a name="prerequisites"></a>Wymagania wstępne

Zestaw Azure Toolkit for IntelliJ wymaga następujących składników oprogramowania:

* Zestaw Java Development Kit (JDK) w wersji 8 lub nowszej, na przykład: [OpenJDK](https://openjdk.java.net/) lub [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Zainstalowane środowisko [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) w wersji Ultimate Edition lub Community Edition

> [!NOTE]
> 
> Strona zestawu [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) w repozytorium wtyczek firmy JetBrains zawiera listę kompilacji, które są zgodne z tym zestawem narzędzi.
> 

<!--
> [!IMPORTANT]
> 
> If you are using the Azure Toolkit for IntelliJ on Windows, the toolkit requires installing the Azure SDK 2.9.6 or later in order to use the Azure emulator. You have two options for installing the Azure SDK:
> 
> * You can download and install the Azure SDK by using the [Web Platform Installer (WebPI)](https://go.microsoft.com/fwlink/?LinkID=252838).
> * If you do not have the Azure SDK installed when you create your first Azure deployment project, you will be prompted to automatically download install the requisite version of the Azure SDK.
> 
> Note that the Azure SDK is only required on Windows.
> 
-->


## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Aby zainstalować zestaw Azure Toolkit for IntelliJ za pomocą okna dialogowego ustawień

1. Uruchom środowisko IntelliJ IDEA.

1. Po otwarciu środowiska IntelliJ IDEA kliknij pozycję **File** (Plik), a następnie kliknij pozycję **Settings** (Ustawienia).
   
   ![Otwieranie okna dialogowego ustawień środowiska IntelliJ IDEA][01a]

1. W oknie dialogowym ustawień kliknij pozycję **Plugins** (Wtyczki), a następnie kliknij pozycję **Browse repositories** (Przeglądaj repozytoria).
   
   ![Okno dialogowe ustawień środowiska IntelliJ IDEA][02a]

1. W oknie dialogowym **Browse Repositories** (Przeglądanie repozytoriów) wpisz wyraz „Azure” w polu wyszukiwania. Wyróżnij pozycję **Azure Toolkit for IntelliJ**, a następnie kliknij pozycję **Install** (Zainstaluj).
   
   ![Wyszukiwanie zestawu Azure Toolkit for IntelliJ][03]
   
   Środowisko IntelliJ IDEA wyświetla postęp instalacji w oknie dialogowym.
   
   ![Postęp instalacji][04]

1. Po zakończeniu instalacji kliknij pozycję **Restart IntelliJ IDEA** (Uruchom ponownie środowisko IntelliJ IDEA).
   
   ![Ponowne uruchamianie środowiska IntelliJ IDEA][05]

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe Settings (Ustawienia).
   
   ![Zamykanie okna dialogowego ustawień środowiska IntelliJ IDEA][06]

1. Po wyświetleniu monitu o ponowne uruchomienie środowiska IntelliJ IDEA lub odroczenie kliknij pozycję **Restart** (Uruchom ponownie).
   
1   ![Ponowne uruchamianie środowiska IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Aby zainstalować zestaw Azure Toolkit for IntelliJ za pomocą ekranu startowego

1. Uruchom środowisko IntelliJ IDEA.

1. Po wyświetleniu ekranu startowego środowiska IntelliJ IDEA kliknij pozycję **Configure** (Konfiguruj), a następnie kliknij pozycję **Plugins** (Wtyczki).
   
   ![Instalowanie wtyczek środowiska IntelliJ IDEA][01b]

1. W oknie dialogowym **Plugins** (Wtyczki) kliknij pozycję **Browse repositories** (Przeglądaj repozytoria).
   
   ![Przeglądanie repozytoriów wtyczek środowiska IntelliJ IDEA][02b]

1. W oknie dialogowym **Browse Repositories** (Przeglądanie repozytoriów) wpisz wyraz „Azure” w polu wyszukiwania. Wyróżnij pozycję **Azure Toolkit for IntelliJ**, a następnie kliknij pozycję **Install** (Zainstaluj).
   
   ![Wyszukiwanie zestawu Azure Toolkit for IntelliJ][03]
   
   Środowisko IntelliJ IDEA wyświetli postęp instalacji w oknie dialogowym.
   
   ![Postęp instalacji][04]

1. Po zakończeniu instalacji kliknij pozycję **Restart IntelliJ IDEA** (Uruchom ponownie środowisko IntelliJ IDEA).
   
   ![Ponowne uruchamianie środowiska IntelliJ IDEA][05]

1. Po wyświetleniu monitu o ponowne uruchomienie środowiska IntelliJ IDEA lub odroczenie kliknij pozycję **Restart** (Uruchom ponownie).
   
   ![Ponowne uruchamianie środowiska IntelliJ IDEA][07]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[01a]: media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: media/azure-toolkit-for-intellij-installation/07-restart-intellij.png
