---
title: Biblioteki zarządzania platformy Azure dla języka Java — informacje o wersji | Microsoft Docs
description: Zapoznaj się z nowościami i informacjami o przełomowych zmianach w bibliotekach zarządzania platformy Azure dla języka Java
keywords: Azure, Java, API, odwołanie, uwagi, aktualizacje, oznaczanie jako przestarzałe
ms.assetid: 1f128cf9-f747-4344-84e1-f9964709deb8
ms.topic: reference
ms.date: 3/06/2016
ms.openlocfilehash: 2ef32414106be2ebfe917e1a5b434ba49d5c86ba
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77000089"
---
# <a name="release-notes"></a>Informacje o wersji 

## <a name="october-5-2017---130"></a>5 października 2017 r. — 1.3.0 

Wersja 1.3.0 jest zgodna wstecznie z poprzednimi wersjami w zakresie korzystania z usług i funkcji, które były ogólnie dostępne w poprzednich (stabilnych) wydaniach.

Przełomowe zmiany w stosunku do wersji zapoznawczej w zakresie tych usług opatrzono adnotacją @Beta.

Jeśli przeprowadzasz migrację kodu do wersji 1.3.0, możesz skorzystać z [tych uwag](https://github.com/Azure/azure-sdk-for-java/blob/master/notes/prepare-for-1.3.0.md), aby przygotować istniejący kod pod kątem wersji 1.3.

### <a name="generally-availabile-in-v13"></a>Ogólnie dostępne w wersji 1.3

Niektóre interfejsy API, które w poprzednich wersjach były w wersji beta, są teraz ogólnie dostępne. W szczególności:

- metody async;
- wszystkie metody w usłudze CDN, które uprzednio były w wersji beta;
- wszystkie metody i interfejsy w usłudze Application Gateway, które uprzednio były w wersji beta.

  Niektóre części biblioteki są ciągle w wersji zapoznawczej. Bieżący stan bibliotek przedstawiono w poniższej tabeli:

Usługa lub funkcja | Ogólnie dostępna | Dostępna w wersji zapoznawczej 
---------|---------|---------|-
Wystąpienia obliczeniowe  | Maszyny wirtualne i rozszerzenia maszyn wirtualnych, zestawy skalowania maszyn wirtualnych, dyski zarządzane   | Usługa kontenera Azure, rejestr kontenerów Azure 
Magazyn   |  Konta magazynu       |    Szyfrowanie     
Baza danych SQL  | Bazy danych, zapory, pule elastyczne              
Networking    |  Sieci wirtualne, interfejsy sieciowe, adresy IP, tabele routingu, sieciowe grupy zabezpieczeń, DNS, menedżery ruchu, bramy aplikacji  |    Moduły równoważenia obciążenia, komunikacja równorzędna w sieci, brama sieci wirtualnej, obserwatory sieciowe 
Więcej usług    |  Resource Manager, Key Vault, Redis, CDN, Batch       |  Aplikacje sieci Web, aplikacje funkcji, Service Bus, Graph RBAC, Cosmos DB, Search  
Podstawy     |   Uwierzytelnianie — rdzeń, metody asynchroniczne, tożsamość usługi zarządzanej      |      |

> Funkcje w wersji zapoznawczej są opatrzone adnotacją `@Beta` obok klasy, interfejsu lub poziomu metody w bibliotekach. Te funkcje mogą ulec zmianie. W przyszłości mogą one zostać zmodyfikowane w dowolnym zakresie, a nawet usunięte.

### <a name="import-with-maven"></a>Importowanie za pomocą narzędzia Maven

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.3.0</version>
</dependency>
```

### <a name="get-help-and-give-feedback"></a>Uzyskiwanie pomocy i przesyłanie opinii

Odwiedź społeczność [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-java-sdk), aby uzyskać pomoc w zakresie korzystania z bibliotek we własnym kodzie. Jeśli wystąpią błędy lub jeśli masz sugestie dotyczące ulepszeń tych bibliotek, zgłaszaj problemy na platformie [GitHub](https://github.com/Azure/azure-sdk-for-java/issues).


