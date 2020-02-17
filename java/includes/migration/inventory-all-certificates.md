---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000929"
---
### <a name="inventory-all-certificates"></a>Utworzenie spisu wszystkich certyfikatów

Zapisz wszystkie certyfikaty używane na potrzeby publicznych punktów końcowych protokołu SSL. Wszystkie certyfikaty na serwerach produkcyjnych można wyświetlić, uruchamiając następujące polecenie:

```bash
keytool -list -v -keystore <path to keystore>
```
