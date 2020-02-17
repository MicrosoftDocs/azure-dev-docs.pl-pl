---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: cc634f89170f4db6f961da91e3eb3abe7393539d
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001076"
---
### <a name="validate-that-the-supported-java-version-works-correctly"></a>Sprawdzanie, czy obsługiwana wersja języka Java działa poprawnie

Wszystkie ścieżki migracji z serwera WebLogic na platformę Azure wymagają konkretnej wersji języka Java, która różni się w zależności od ścieżki. Musisz sprawdzić, czy Twoja aplikacja może działać prawidłowo, używając tej obsługiwanej wersji.

Aby uzyskać informacje na temat bieżącej wersji, zaloguj się na serwerze produkcyjnym i uruchom to polecenie:

```bash
java -version
```

> [!NOTE]
> W przypadku migracji do serwera WebLogic na maszynach wirtualnych platformy Azure wymagania dotyczące konkretnych wersji języka Java są określane przez środowisko Java zainstalowane wstępnie na maszynach wirtualnych.
