---
title: Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for Eclipse
description: Dowiedz się, jak zalogować się do platformy Microsoft Azure za pomocą zestawu narzędzi Azure Toolkit for Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: fef2dde02547004e6abd28d2a9355bfd59d4dbc9
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999641"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Instrukcje logowania dotyczące zestawu narzędzi Azure Toolkit for Eclipse

Zestaw narzędzi Azure Toolkit for Eclipse zapewnia dwie metody logowania do konta platformy Azure:

  - [Logowanie do konta platformy Azure za pomocą funkcji logowania do urządzenia](#sign-in-to-your-azure-account-by-device-login)
  - [Logowanie do konta platformy Azure za pomocą jednostki usługi](#sign-in-to-your-azure-account-by-service-principal)

Dostępne są również metody [**wylogowywania**](#sign-out-of-your-azure-account).

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Logowanie do konta platformy Azure za pomocą funkcji logowania do urządzenia

Aby zalogować się do konta platformy Azure za pomocą funkcji logowania do urządzenia, wykonaj następujące czynności:

1. Otwórz projekt w środowisku Eclipse.

2. Kliknij pozycję **Tools** (Narzędzia), następnie kliknij pozycję **Azure**, a następnie kliknij pozycję **Sign In** (Zaloguj).
   ![Menu środowiska Eclipse na potrzeby logowania do platformy Azure][I01]

3. W oknie **logowania do platformy Azure** wybierz pozycję **Device Login** (Logowanie do urządzenia), a następnie kliknij pozycję **Sign in** (Zaloguj).

   ![Okno logowania do platformy Azure z wybraną opcją logowania do urządzenia][I02]

4. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij pozycję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe Azure Login (Logowanie Azure)][I03]

> [!NOTE]
>
> Jeśli przeglądarka nie zostanie otwarta, skonfiguruj środowisko Eclipse w celu używania zewnętrznej przeglądarki, takiej jak Internet Explorer, Firefox lub Chrome:
>
> 1. Wybierz kolejno pozycje Preferences -> General -> Web Browser -> Use external web browser in Eclipse (Preferencje -> Ogólne -> Przeglądarka internetowa -> Używaj zewnętrznej przeglądarki internetowej w środowisku Eclipse)
>
> 2. Wybierz przeglądarkę, której chcesz używać
>

5. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania urządzenia][I04]

6. Na koniec w oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Logowanie do konta platformy Azure za pomocą jednostki usługi

Ta sekcja zawiera instrukcję tworzenia pliku poświadczeń zawierającego dane jednostki usługi. Po zakończeniu tego procesu środowisko Eclipse używa pliku poświadczeń do automatycznego logowania użytkownika do platformy Azure po otwarciu projektu.

1. Otwórz projekt w środowisku Eclipse.

2. Kliknij pozycję **Tools** (Narzędzia), następnie kliknij pozycję **Azure**, a następnie kliknij pozycję **Sign In** (Zaloguj).
   ![Polecenie Azure Sign In (Logowanie do platformy Azure) na platformie Eclipse][A01]

3. W oknie **logowania do platformy Azure** wybierz pozycję **Service Principal** (Jednostka usługi). Jeśli nie masz jeszcze pliku uwierzytelniania jednostki usługi, kliknij pozycję **New** (Nowy), aby go utworzyć. W przeciwnym razie możesz kliknąć pozycję **Browse** (Przeglądaj), aby go otworzyć i przejść do kroku 8.

   ![Okno logowania do platformy Azure z wybraną jednostką usługi][A02]

4. W oknie dialogowym **Azure Device Login** (Logowanie Azure do urządzenia) kliknij pozycję **Copy&Open** (Kopiuj i otwórz).

   ![Okno dialogowe Azure Login (Logowanie Azure)][A08]

> [!NOTE]
>
> Jeśli przeglądarka nie zostanie otwarta, skonfiguruj środowisko Eclipse w celu używania zewnętrznej przeglądarki, takiej jak IE lub Chrome:
>
> 1. Wybierz kolejno pozycje Preferences -> General -> Web Browser -> Use external web browser in Eclipse (Preferencje -> Ogólne -> Przeglądarka internetowa -> Używaj zewnętrznej przeglądarki internetowej w środowisku Eclipse)
>
> 2. Wybierz przeglądarkę, której chcesz używać
>

5. W przeglądarce wklej kod urządzenia (skopiowany po kliknięciu pozycji **Copy&Open** (Kopiuj i otwórz) w ostatnim kroku), a następnie kliknij przycisk **Next** (Dalej).

   ![Przeglądarka z oknem logowania urządzenia][A03]

6. W oknie **Create Authentication Files** (Utwórz pliki uwierzytelniania) wybierz subskrypcje do użycia i katalog docelowy, a następnie kliknij przycisk **Start** (Uruchom).

   ![Okno Create Authentication Files (Utwórz pliki uwierzytelniania)][A04]

7. Po pomyślnym utworzeniu plików kliknij przycisk **OK** w oknie dialogowym **Service Principal Creation Status** (Stan tworzenia jednostki usługi).

   ![Okno dialogowe Service Principal Creation Status (Stan tworzenia jednostki usługi)][A05]

8. Adres utworzonego pliku zostanie wypełniony automatycznie w oknie **logowania do platformy Azure**. Kliknij pozycję **Sign in** (Zaloguj).

   ![Okno dialogowe logowania do platformy Azure][A06]

9. Na koniec w oknie dialogowym **Select Subscriptions** (Wybieranie subskrypcji) wybierz subskrypcje do użycia, a następnie kliknij przycisk **OK**.

   ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)][A07]

## <a name="sign-out-of-your-azure-account"></a>Wylogowywanie się z konta platformy Azure

Po skonfigurowaniu konta za pomocą poprzednich kroków przy każdym uruchomieniu środowiska Eclipse nastąpi automatyczne zalogowanie. Jeśli jednak zachcesz wylogować się z konta platformy Azure, wykonaj następujące czynności.

1. W środowisku Eclipse kliknij pozycję **Tools** (Narzędzia), następnie kliknij pozycję **Azure**, a następnie kliknij pozycję **Sign Out** (Wyloguj).

   ![Menu środowiska Eclipse na potrzeby wylogowania z platformy Azure][L01]

2. Po wyświetleniu okna dialogowego **wylogowania z platformy Azure** kliknij przycisk **Yes** (Tak).

   ![Okno dialogowe wylogowania][L02]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->


<!-- IMG List -->

[I01]: media/azure-toolkit-for-eclipse-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-eclipse-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-eclipse-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-eclipse-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-eclipse-sign-in-instructions/I05.png

[A01]: media/azure-toolkit-for-eclipse-sign-in-instructions/A01.png
[A02]: media/azure-toolkit-for-eclipse-sign-in-instructions/A02.png
[A03]: media/azure-toolkit-for-eclipse-sign-in-instructions/A03.png
[A04]: media/azure-toolkit-for-eclipse-sign-in-instructions/A04.png
[A05]: media/azure-toolkit-for-eclipse-sign-in-instructions/A05.png
[A06]: media/azure-toolkit-for-eclipse-sign-in-instructions/A06.png
[A07]: media/azure-toolkit-for-eclipse-sign-in-instructions/A07.png
[A08]: media/azure-toolkit-for-eclipse-sign-in-instructions/A08.png

[L01]: media/azure-toolkit-for-eclipse-sign-in-instructions/L01.png
[L02]: media/azure-toolkit-for-eclipse-sign-in-instructions/L02.png
[L03]: media/azure-toolkit-for-eclipse-sign-in-instructions/L03.png
