---
title: Wyświetlanie zawartości narzędzia Javadoc w środowisku Eclipse
titleSuffix: Azure Libraries for Java
description: Jak wyświetlić zawartość narzędzia Javadoc dla bibliotek platformy Azure w środowisku Eclipse.
documentationcenter: java
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: ecd491031522fdf2a9e06ff4104f7191f4efc59e
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999578"
---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Wyświetlanie zawartości narzędzia Javadoc w środowisku Eclipse dla pakietu bibliotek platformy Azure w języku Java

Zawartość narzędzia Javadoc dla bibliotek platformy Azure w języku Java można wyświetlić w środowisku Eclipse, kojarząc zawartość Javadoc z bibliotekami platformy Azure dla języka Java. Poniższe kroki pokazują, jak używać tej funkcji w środowisku Eclipse.

W tej procedurze przyjęto założenie, że biblioteka platformy Azure dla języka Java została już dodana do ścieżki kompilacji.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Aby wyświetlić zawartość narzędzia Javadoc w środowisku Eclipse dla bibliotek platformy Azure dla języka Java

1. W obszarze Project Explorer (Eksplorator projektów) środowisku Eclipse, w sekcji **Referenced Libraries** (Przywoływane biblioteki) projektu otwórz menu kontekstowe pliku JAR biblioteki platformy Azure dla języka Java. Na przykład **microsoft-windowsazure-api-0.1.0.jar** (numer wersji może być inny w zależności od zainstalowanej wersji).

1. Kliknij pozycję **Właściwości**.

1. W oknie dialogowym **Properties** (Właściwości), w okienku po lewej stronie kliknij pozycję **Javadoc Location** (Lokalizacja narzędzia Javadoc). Zostanie wyświetlone okno dialogowe **Javadoc Location** (Lokalizacja narzędzia Javadoc).

1. Możesz określić **adres URL narzędzia Javadoc** lub wybrać **narzędzie Javadoc w archiwum**.

   * Jeśli zdecydujesz się określić **adres URL narzędzia Javadoc**, użyj adresów URL, takich jak **https://dl.windowsazure.com/javadoc** lub **https://dl.windowsazure.com/storage/javadoc** .

   * Jeśli zdecydujesz się używać **narzędzia Javadoc w archiwum**, możesz określić plik zewnętrzny lub plik obszaru roboczego.

   Dokonaj wyboru i przejrzyj/zweryfikuj opcje zgodnie z potrzebami. Poniższy przykład kojarzy biblioteki platformy Azure dla języka Java z odpowiednim plikiem JAR narzędzia Javadoc, który został pobrany lokalnie do folderu o nazwie **c:\MyAzureJARs**.

   ![Wyświetl zawartość narzędzia Javadoc.][ic553487]

1. *Krok opcjonalny*: Kliknij pozycję **Validate** (Waliduj). W tym miejscu można zobaczyć potencjalne problemy dotyczące pliku JAR narzędzia Javadoc.

1. Kliknij przycisk **OK**.

Po skojarzeniu z biblioteką zawartość narzędzia Javadoc powinna być wyświetlana w środowisku IDE Eclipse. Jeśli na przykład w kodzie zdefiniowano obiekt `blob` typu `CloudBlockBlob`, poniżej przedstawiono przykład zawartości narzędzia Javadoc, która pojawia się po wpisaniu ciągu `blob.acquireLease` w kodzie:

![Wyświetl zawartość narzędzia Javadoc.][ic553488]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

<!-- IMG List -->

[ic553487]: media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png
