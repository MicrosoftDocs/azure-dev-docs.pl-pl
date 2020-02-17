---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001062"
---
### <a name="inventory-certificates"></a>Certyfikaty spisu

Udokumentuj wszystkie certyfikaty używane w przypadku publicznych punktów końcowych protokołu SSL lub komunikacji z bazami danych zaplecza i innymi systemami. Wszystkie certyfikaty na serwerach produkcyjnych można wyświetlić, uruchamiając następujące polecenie:

```bash
keytool -list -v -keystore <path to keystore>
```
