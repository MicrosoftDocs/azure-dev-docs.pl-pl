---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: e37d0337361ce742ce7f7994ab634e63bc4b2ead
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000915"
---
### <a name="inventory-secrets"></a>Utworzenie spisu wpisów tajnych

#### <a name="passwords-and-secure-strings"></a>Hasła i bezpieczne ciągi

Sprawdź wszystkie pliki właściwości i konfiguracji na serwerach produkcyjnych pod kątem wszelkich ciągów wpisów tajnych i haseł. Sprawdź pliki *server.xml* i *context.xml* w katalogu *$CATALINA_BASE/conf*. Możesz również znaleźć pliki konfiguracji zawierające hasła lub poświadczenia wewnątrz aplikacji. Mogą one obejmować plik *META-INF/context.xml* oraz, w przypadku aplikacji Spring Boot, pliki *application.properties* lub *application.yml*.
