---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 478059f598b9c39609a85647945ebef3df3cb7ec
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000789"
---
### <a name="inventory-persistence-usage"></a>Utworzenie spisu użycia trwałości

Każde użycie systemu plików na serwerze aplikacji będzie wymagało ponownej konfiguracji lub, w rzadkich przypadkach, zmiany architektury. Można zidentyfikować niektóre lub wszystkie z następujących scenariuszy.

#### <a name="read-only-static-content"></a>Zawartość statyczna tylko do odczytu

Jeśli aplikacja obsługuje obecnie zawartość statyczną (na przykład za pośrednictwem integracji z systemem Apache), będzie potrzebna alternatywna lokalizacja dla tej zawartości statycznej. Warto rozważyć przeniesienie zawartości statycznej do usługi Azure Blob Storage i dodanie usługi Azure CDN, aby zapewnić błyskawiczne pobieranie na całym świecie. Aby uzyskać więcej informacji, zobacz [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website) oraz [przewodnik Szybki start: integracja konta magazynu platformy Azure z usługą Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn#enable-azure-cdn-for-the-storage-account).

#### <a name="dynamically-published-static-content"></a>Dynamicznie publikowana zawartość statyczna

Jeśli aplikacja zezwala na zawartość statyczną, która została przekazana/utworzona przez aplikację, ale pozostaje niezmienna po jej utworzeniu, możesz użyć usług Azure Blob Storage i Azure CDN, jak opisano powyżej, oraz usługi Azure Function do obsługiwania przekazywania i odświeżania usługi CDN. Udostępniliśmy przykładową implementację do użycia w temacie [Przekazywanie zawartości statycznej i jej wstępne ładowanie w usłudze CDN za pomocą usługi Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
