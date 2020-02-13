---
title: Instalowanie zestawu narzędzi Azure Toolkit for Eclipse
description: Dowiedz się, jak zainstalować wtyczkę Azure Toolkit for Eclipse, aby tworzyć i wdrażać aplikacje w chmurze na platformie Azure.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 8a72ce4b8ceea8a6b5ba03b2800f46220f8c70c5
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999585"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalowanie zestawu narzędzi Azure Toolkit for Eclipse

Istnieją dwa sposoby instalowania zestawu narzędzi Azure Toolkit for Eclipse:

  - [Witryna Eclipse Marketplace](#eclipse-marketplace)
  - [Instalowanie nowego oprogramowania](#install-new-software)

> [!NOTE] 
> 
> Zestaw Azure Toolkit for Eclipse to projekt open-source, którego kod źródłowy jest dostępny w ramach licencji MIT w witrynie projektu w serwisie GitHub pod następującym adresem URL: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [azure-toolkit-for-eclipse-basic-prerequisites](../includes/azure-toolkit-for-eclipse-basic-prerequisites.md)]

## <a name="eclipse-marketplace"></a>Witryna Eclipse Marketplace

1. Przeciągnij poniższy przycisk do działającego obszaru roboczego środowiska Eclipse.

    [![Przeciągnij do działającego obszaru roboczego środowiska Eclipse*. *Wymaga rozwiązania Eclipse Marketplace Client](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Przeciągnij do działającego obszaru roboczego środowiska Eclipse*. *Wymaga rozwiązania Eclipse Marketplace Client")

2. Można również wyszukać i zainstalować **wtyczkę Azure Toolkit for Eclipse** w witrynie **Help/Eclipse Marketplace** (Pomoc/Eclipse Marketplace).

    ![Portal Marketplace](./media/azure-toolkit-for-eclipse-installation/marketplace.png)

## <a name="install-new-software"></a>Instalowanie nowego oprogramowania

1. Uruchom środowisko Eclipse.

1. Kliknij menu **Help** (Pomoc), a następnie kliknij pozycję **Install New Software** (Zainstaluj nowe oprogramowanie), jak pokazano na poniższej ilustracji.

   ![Instalowanie zestawu narzędzi Azure Toolkit for Eclipse][01]

1. W oknie dialogowym **Available Software** (Dostępne oprogramowanie) w polu **Work with** (Praca z) wpisz ciąg `http://dl.microsoft.com/eclipse/` i naciśnij klawisz **Enter**.

1. W okienku **Name** (Nazwa) zaznacz pozycję **Azure Toolkit for Java** i usuń zaznaczenie pola wyboru **Contact all update sites during install to find required software** (Skontaktuj się ze wszystkimi witrynami aktualizacji podczas instalacji, aby znaleźć wymagane oprogramowanie). Ekran powinien wyglądać podobnie do następującego:

   ![Instalowanie zestawu narzędzi Azure Toolkit for Eclipse][02]

1. Po rozwinięciu węzła **Azure Toolkit for Eclipse** pojawi się lista składników, które zostaną zainstalowane, na przykład:

   | Cecha | Opis | 
   |---|---| 
   | **Application Insights Plugin for Java** | Umożliwia korzystanie z usług rejestrowania i analizy danych telemetrycznych platformy Azure dla wystąpień aplikacji i serwerów. | 
   | **Azure Common Plugin** | Udostępnia typowe funkcje, których potrzebują inne składniki zestawu narzędzi. | 
   | **Azure Container Tools for Eclipse** | Umożliwia tworzenie i wdrażanie pliku WAR jako kontenera platformy Docker na maszynie platformy Docker. | 
   | **Azure Containers for Eclipse** | Umożliwia wdrożenie artefaktu WAR lub JAR jako kontenera platformy Docker na maszynie wirtualnej platformy Azure. | 
   | **Azure Explorer for Eclipse** | Udostępnia interfejs w stylu eksploratora służący do zarządzania zasobami platformy Azure. | 
   | **Sterownik JDBC firmy Microsoft w wersji 6.1 dla programu SQL Server** | Udostępnia interfejs API JDBC dla programu SQL Server i usługę Microsoft Azure SQL Database dla platformy Java Platform Enterprise Edition 8. | 
   | **Pakiet związany z bibliotekami platformy Microsoft Azure dla języka Java** | Udostępnia interfejsy API umożliwiające uzyskiwanie dostępu do usług platformy Microsoft Azure, takich jak Storage, Service Bus, usługa środowiska uruchomieniowego itp. | 

1. Kliknij przycisk **Dalej**. (W przypadku wystąpienia nietypowych opóźnień podczas instalowania zestawu narzędzi upewnij się, że zaznaczenie pola **Contact all update sites during install to find required software** (Skontaktuj się ze wszystkimi witrynami aktualizacji podczas instalacji, aby znaleźć wymagane oprogramowanie) zostało usunięte).

1. W oknie dialogowym **Install Details** (Szczegóły instalacji) kliknij przycisk **Next** (Dalej).

   ![Przeglądanie szczegółów instalacji][03]

1. W oknie dialogowym **Review Licenses** (Przeglądanie licencji) przejrzyj warunki umów licencyjnych. Jeśli akceptujesz warunki umów licencyjnych, kliknij pozycję **I accept the terms of the license agreements** (Akceptuję warunki umów licencyjnych), a następnie kliknij przycisk **Finish** (Zakończ). (W pozostałych krokach przyjęto założenie, że akceptujesz warunki umów licencyjnych. Jeśli nie akceptujesz warunków umowy licencyjnej, zakończ proces instalacji).

   ![Przeglądanie licencji][04]

   Środowisko Eclipse pobierze i zainstaluje wymagane pakiety.

   ![Postęp instalacji][05]

1. Jeśli zostanie wyświetlony monit o ponowne uruchomienie środowiska Eclipse w celu ukończenia instalacji, kliknij przycisk **Yes** (Tak).

   ![Monit o ponowne uruchomienie][06]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[01]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png
