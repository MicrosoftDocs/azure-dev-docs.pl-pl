---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 029277ac75c455041d26f160b1f0c16a5675368d
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001006"
---
### <a name="validate-whether-and-how-the-file-system-is-used"></a>Sprawdzanie, czy i jak jest używany system plików

Pod względem trwałości, uruchamiania i zamykania systemy plików maszyn wirtualnych działają tak samo jak lokalne systemy plików. Mimo to, ważne jest, aby znać wymagania systemu plików i zapewnić maszynom wirtualnym odpowiedni rozmiar oraz wydajność magazynu.

#### <a name="read-only-static-content"></a>Zawartość statyczna tylko do odczytu

Jeśli aplikacja aktualnie obsługuje zawartość statyczną, potrzebna jest dodatkowa lokalizacja. Warto rozważyć przeniesienie zawartości statycznej do usługi Azure Blob Storage i dodanie usługi Azure CDN, aby zapewnić błyskawiczne pobieranie na całym świecie. Aby uzyskać więcej informacji, zobacz [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website) oraz [przewodnik Szybki start: integracja konta magazynu platformy Azure z usługą Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Dynamicznie publikowana zawartość statyczna

Jeśli aplikacja zezwala na zawartość statyczną, która została przekazana/utworzona przez aplikację, ale pozostaje niezmienna po jej utworzeniu, możesz użyć usług Azure Blob Storage i Azure CDN, jak opisano powyżej, oraz usługi Azure Function do obsługiwania przekazywania i odświeżania usługi CDN. Udostępniliśmy przykładową implementację do użycia w temacie [Przekazywanie zawartości statycznej i jej wstępne ładowanie w usłudze CDN za pomocą usługi Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
