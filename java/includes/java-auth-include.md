---
ms.openlocfilehash: 0a9b06932baa51c3cf003a4485a3a25261ffe91d
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "76998843"
---
Utwórz [plik uwierzytelniania](../java-sdk-azure-authenticate.md#mgmt-file) i wyeksportuj zmienną środowiskową `AZURE_AUTH_LOCATION` w wierszu polecenia z pełną ścieżką do pliku.

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azure.auth
```

Plik uwierzytelniania służy do konfigurowania punktu wejścia obiektu `Azure` punktu wejścia używanego przez biblioteki zarządzania do definiowania, tworzenia i konfigurowania zasobów platformy Azure.

```java
// pull in the location of the security file from the environment 
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```

[Dowiedz się więcej](../java-sdk-azure-authenticate.md#mgmt-auth) na temat opcji uwierzytelniania w przypadku korzystania z bibliotek zarządzania platformy Azure dla języka Java.