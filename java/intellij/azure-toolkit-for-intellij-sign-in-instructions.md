---
title: Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for IntelliJ
description: Dowiedz się, jak zalogować się do platformy Microsoft Azure za pomocą zestawu narzędzi Azure Toolkit for IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 345880fae8b3ef1c72833327263b76cffc96bf78
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77000215"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for IntelliJ

Po [zainstalowaniu](https://www.jetbrains.com/help/idea/managing-plugins.html) zestaw narzędzi [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) zapewnia dwie metody logowania do konta platformy Azure:

  - [Logowanie do konta platformy Azure za pomocą funkcji logowania do urządzenia](#sign-in-to-your-azure-account-by-device-login)
  - [Logowanie do konta platformy Azure za pomocą jednostki usługi](#sign-in-to-your-azure-account-by-service-principal)

Dostępne są również metody [**wylogowywania**](#sign-out-of-your-azure-account).

[!INCLUDE [azure-toolkit-for-intellij-basic-prerequisites](../includes/azure-toolkit-for-intellij-basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Logowanie do konta platformy Azure za pomocą funkcji logowania do urządzenia

Aby zalogować się do konta platformy Azure za pomocą funkcji logowania do urządzenia, wykonaj następujące czynności:

1. Otwórz projekt w środowisku IntelliJ IDEA.

2. Otwórz pasek boczny **Eksplorator Azure** i kliknij ikonę **Logowanie Azure** w górnej części paska (lub z menu IDEA **Tools/Azure/Azure Sign in** [Narzędzia/Azure/Logowanie Azure]).

   ![Polecenie IntelliJ Azure Sign In (Logowanie Azure)][I01]

3. W oknie **logowania do platformy Azure** wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj się).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][I02]

4. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij opcję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe Azure Login (Logowanie Azure)][I03]

5. W przeglądarce wklej kod urządzenia, skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku, a następnie kliknij przycisk **Dalej**.

   ![Okno logowania w przeglądarce][I04]

6. W oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Logowanie do konta platformy Azure za pomocą jednostki usługi

Ta sekcja zawiera instrukcję tworzenia pliku poświadczeń zawierającego dane jednostki usługi. Po zakończeniu tego procesu IntelliJ używa pliku poświadczeń do automatycznego logowania użytkownika do platformy Azure po otwarciu projektu.

1. Otwórz projekt w środowisku IntelliJ IDEA.

1. Otwórz pasek boczny **Eksplorator Azure** i kliknij ikonę **Logowanie Azure** w górnej części paska (lub z menu IDEA **Tools/Azure/Azure Sign in** [Narzędzia/Azure/Logowanie Azure]).
   ![Polecenie IntelliJ Azure Sign In (Logowanie Azure)][A01]

1. W oknie **logowania do platformy Azure** wybierz pozycję **Service Principal** (Jednostka usługi), a następnie kliknij pozycję **New** (Nowa).

   ![Okno logowania do platformy Azure z wybraną jednostką usługi][A02]

1. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij opcję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe Azure Login (Logowanie Azure)][A03]

1. W przeglądarce wklej kod urządzenia, skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku, a następnie kliknij przycisk **Dalej**.

   ![Okno logowania w przeglądarce][A04]

1. W oknie **Create Authentication Files** (Utwórz pliki uwierzytelniania) wybierz subskrypcje do użycia, katalog docelowy, po czym kliknij przycisk **Start** (Uruchom).

   ![Okno Create Authentication Files (Utwórz pliki uwierzytelniania)][A05]

1. Po pomyślnym utworzeniu plików kliknij przycisk **OK** w oknie dialogowym **Service Principal Creation Status** (Stan tworzenia jednostki usługi).

   ![Okno dialogowe Service Principal Creation Status (Stan tworzenia jednostki usługi)][A06]

1. W oknie **logowania do platformy Azure** kliknij przycisk **Sign in** (Zaloguj). 

   ![Okno dialogowe logowania do platformy Azure][A07]

1. W oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][A08]

> Po utworzeniu pliku uwierzytelniania jednostki usługi możesz rozpocząć od kroku 8, wybrać plik uwierzytelniania i zalogować się.

## <a name="sign-out-of-your-azure-account"></a>Wylogowywanie się z konta platformy Azure

Po skonfigurowaniu konta w poprzednich krokach nastąpi automatyczne zalogowanie po każdym uruchomieniu IntelliJ IDEA. Jeśli jednak chcesz się wylogować z konta platformy Azure, wykonaj następujące czynności.

* W środowisku IntelliJ IDEA otwórz pasek boczny Eksploratora Azure, kliknij ikonę **Azure Sign Out** (Wylogowanie z platformy Azure) i potwierdź. Możesz też użyć menu IDEA **Tools/Azure/Azure Sign Out** (Narzędzia/Azure/Wylogowanie z platformy Azure).

   ![Polecenie IntelliJ wylogowania z platformy Azure][L01]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-intellij-additional-resources](../includes/azure-toolkit-for-intellij-additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-intellij-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-intellij-sign-in-instructions/I05.png

[A01]: media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: media/azure-toolkit-for-intellij-sign-in-instructions/A08.png
[A09]: media/azure-toolkit-for-intellij-sign-in-instructions/A09.png

[L01]: media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: media/azure-toolkit-for-intellij-sign-in-instructions/L03.png
