---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 2fc4e3b43a051103e7aea6aa77f66666ca454910
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000852"
---
<!-- Included in "### Switch to a supported platform" sections that have different (required) intro paragraphs. For example:

### Switch to a supported platform

App Service offers specific versions of Java SE. To ensure compatibility, migrate your application to one of the supported versions of in its current environment before you proceed with any of the remaining steps. Be sure to fully test the resulting configuration. Use the latest stable release of your Linux distribution in such tests.

-->

> [!NOTE]
> Ta weryfikacja jest szczególnie ważna, jeśli bieżący serwer działa na nieobsługiwanym zestawie JDK (na przykład Oracle JDK lub IBM OpenJ9).

Aby uzyskać informacje na temat bieżącej wersji języka Java, zaloguj się na serwerze produkcyjnym i uruchom następujące polecenie:

```bash
java -version
```

Aby uzyskać bieżącą wersję używaną przez usługę Azure App Service, pobierz [Zulu 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk), jeśli zamierzasz używać środowiska uruchomieniowego Java 8, lub [Zulu 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts&architecture=x86-64-bit&package=jdk), jeśli zamierzasz używać środowiska uruchomieniowego Java 11.
